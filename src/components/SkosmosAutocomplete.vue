<template>
  <div class="relative w-full">
    <div v-if="loading"
      class="flex items-center gap-2 text-sm text-base-content/50 h-12 px-4 border border-base-200 rounded-lg">
      <span class="loading loading-spinner loading-xs"></span> Loading terms...
    </div>
    <div v-else-if="error" class="text-sm text-error h-12 px-4 flex items-center border border-error rounded-lg">
      Failed to load vocabulary.
    </div>
    <div v-else>
      <!-- Normal dropdown if <= 15 terms -->
      <select v-if="terms.length <= 15" v-model="internalValue" class="select select-bordered w-full">
        <option value="" disabled>Select an option</option>
        <option v-for="term in terms" :key="term.uri" :value="formatTerm(term)">
          {{ term.prefLabel }}
        </option>
      </select>

      <!-- Autocomplete if > 15 terms -->
      <div v-else class="dropdown w-full" :class="{ 'dropdown-open': showDropdown }">
        <input type="text" class="input input-bordered w-full" v-model="searchQuery" @input="onSearch" @focus="onFocus"
          @blur="onBlur" :placeholder="props.placeholder" />
        <ul tabindex="0"
          class="dropdown-content z-[1] menu p-2 shadow bg-base-100 rounded-box w-full max-h-64 overflow-y-auto"
          v-show="showDropdown">
          <li v-for="term in filteredTerms" :key="term.uri">
            <a @mousedown.prevent="selectTerm(term)">{{ term.prefLabel }}</a>
          </li>
          <li v-if="filteredTerms.length === 0" class="disabled">
            <a>No matches found</a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const props = defineProps({
  modelValue: {
    type: [String, Array],
    default: ''
  },
  vocabUrl: {
    type: String,
    required: true
  },
  placeholder: {
    type: String,
    required: true
  }
});

const emit = defineEmits(['update:modelValue']);

const terms = ref([]);
const loading = ref(true);
const error = ref(false);

const internalValue = ref(props.modelValue);
const searchQuery = ref('');
const showDropdown = ref(false);

watch(() => props.modelValue, (newVal) => {
  internalValue.value = newVal;
  if (terms.value.length > 15 && newVal) {
    const match = String(newVal).match(/^(.*?) <.*>$/);
    if (match) {
      searchQuery.value = match[1];
    } else {
      searchQuery.value = newVal;
    }
  }
});

watch(internalValue, (newVal) => {
  emit('update:modelValue', newVal);
});

const formatTerm = (term) => {
  return `${term.prefLabel} <${term.uri}>`;
};

const fetchTree = async (url, visited = new Set()) => {
  try {
    const response = await fetch(url);
    if (!response.ok) return [];
    const data = await response.json();

    // Parse skosmos narrower terms response
    let fetchedTerms = [];
    if (data.narrower && Array.isArray(data.narrower)) {
      fetchedTerms = data.narrower;
    } else if (data.results && Array.isArray(data.results)) {
      fetchedTerms = data.results;
    } else if (Array.isArray(data)) {
      fetchedTerms = data;
    } else if (data.concept && data.concept.narrower) {
      fetchedTerms = data.concept.narrower;
    }

    let allTerms = [];

    const childPromises = fetchedTerms.map(async (t) => {
      if (visited.has(t.uri)) return [];
      visited.add(t.uri);

      let terms = [{
        uri: t.uri,
        prefLabel: t.prefLabel || t.label || t.uri
      }];

      // If we don't know for sure it has no children, fetch recursively
      const mightHaveChildren = t.hasChildren !== false && t.hasNarrower !== false;

      if (mightHaveChildren) {
        try {
          const nextUrl = new URL(url);
          nextUrl.searchParams.set('uri', t.uri);
          const childTerms = await fetchTree(nextUrl.toString(), visited);
          terms = terms.concat(childTerms);
        } catch (err) {
          console.warn('Could not fetch children for', t.uri, err);
        }
      }
      return terms;
    });

    const results = await Promise.all(childPromises);
    for (const res of results) {
      allTerms = allTerms.concat(res);
    }

    return allTerms;
  } catch (err) {
    console.warn('Error fetching tree at url', url, err);
    return [];
  }
};

const fetchTerms = async () => {
  try {
    loading.value = true;
    error.value = false;

    terms.value = await fetchTree(props.vocabUrl);

    if (terms.value.length > 15 && props.modelValue) {
      const match = String(props.modelValue).match(/^(.*?) <.*>$/);
      searchQuery.value = match ? match[1] : props.modelValue;
    }
  } catch (e) {
    console.error('Error fetching Skosmos vocabulary:', e);
    error.value = true;
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  if (props.vocabUrl) {
    fetchTerms();
  }
});

const filteredTerms = computed(() => {
  if (!searchQuery.value) {
    return terms.value.slice(0, 10);
  }
  const query = searchQuery.value.toLowerCase();
  const filtered = terms.value.filter(t => t.prefLabel.toLowerCase().includes(query));
  return filtered.slice(0, 10);
});

const onSearch = () => {
  showDropdown.value = true;
};

const onFocus = () => {
  showDropdown.value = true;
};

const onBlur = () => {
  setTimeout(() => {
    showDropdown.value = false;
    if (internalValue.value) {
      const match = String(internalValue.value).match(/^(.*?) <.*>$/);
      searchQuery.value = match ? match[1] : internalValue.value;
    } else {
      searchQuery.value = '';
    }
  }, 150);
};

const selectTerm = (term) => {
  const formatted = formatTerm(term);
  internalValue.value = formatted;
  searchQuery.value = term.prefLabel;
  showDropdown.value = false;
};
</script>
