<template>
  <main class="flex flex-col items-center w-full min-h-screen relative drawer">
    <input id="my-drawer-1" type="checkbox" class="drawer-toggle" />
    <div
      class="relative sticky top-0 bg-base-100/95 backdrop-blur z-50 w-full flex justify-center p-4 mb-8 text-4xl font-semibold border-b">
      <p class="text-accent">HER</p>
      itage&nbsp;
      <p class="text-accent">M</p>
      etadata&nbsp;
      <p class="text-accent">E</p>
      ntry&nbsp;
      <p class="text-accent">S</p>
      ystem
      <div class="flex gap-4 absolute right-4 top-1/2 -translate-y-1/2">
        <label class="swap cursor-pointer">
          <input type="checkbox" false-value="en" true-value="de" v-model="currentLocale" />

          <svg class="swap-on fill-current" xmlns="http://www.w3.org/2000/svg" width="24" height="24"
            viewBox="0 0 24 24">
            <defs>
              <clipPath id="de-clip">
                <rect x="0" y="4" width="24" height="16" rx="1.5" />
              </clipPath>
            </defs>
            <g clip-path="url(#de-clip)">
              <rect x="0" y="4" width="24" height="5.33" fill="#000000" />
              <rect x="0" y="9.33" width="24" height="5.34" fill="#FF0000" />
              <rect x="0" y="14.67" width="24" height="5.33" fill="#FFCE00" />
            </g>
          </svg>

          <svg class="swap-off fill-current" xmlns="http://www.w3.org/2000/svg" width="24" height="24"
            viewBox="0 0 24 24">
            <defs>
              <clipPath id="uk-clip">
                <rect x="0" y="4" width="24" height="16" rx="1.5" />
              </clipPath>
            </defs>
            <g clip-path="url(#uk-clip)">
              <rect x="0" y="4" width="24" height="16" fill="#012169" />

              <path d="M0,4 L24,20 M24,4 L0,20" stroke="#FFFFFF" stroke-width="2.5" />
              <path d="M0,4 L24,20 M24,4 L0,20" stroke="#C8102E" stroke-width="1.2" />

              <path d="M12,4 V20 M0,12 H24" stroke="#FFFFFF" stroke-width="4" />
              <path d="M12,4 V20 M0,12 H24" stroke="#C8102E" stroke-width="2.4" />
            </g>
          </svg>
        </label>

        <label class="flex cursor-pointer gap-2">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
          </svg>
          <input type="checkbox" value="corporate" class="toggle theme-controller" />
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none"
            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="5" />
            <path
              d="M12 1v2M12 21v2M4.2 4.2l1.4 1.4M18.4 18.4l1.4 1.4M1 12h2M21 12h2M4.2 19.8l1.4-1.4M18.4 5.6l1.4-1.4" />
          </svg>
        </label>
      </div>
    </div>

    <div class="flex relative w-full max-w-7xl mx-auto px-4 items-start">
      <div class="sticky top-28 left-0 w-1/5 flex flex-col items-center p-4 bg-base-100/95 backdrop-blur h-full">
        <!--    <ul class="steps steps-vertical cursor-pointer mb-6">
          <li
            v-for="(step, index) in currentLocale == 'de' ? stepsDE : stepsEN"
            :key="index"
            class="step px-2 transition-all duration-300"
            :class="{ 'step-primary': index <= activeStepIndex }"
            @click="scrollToSection(index)"
          >
            {{ step }}
          </li>
        </ul>-->

        <ul class="w-full menu p-0 [&_li>*]:p-1">
          <li v-for="(step, index) in steps" :key="index" :tabindex="index" class="collapse mb-2" :class="[
            step.id !== 'files' ? 'collapse-arrow' : '',
            index === activeStepIndex ? 'collapse-open' : 'collapse-close',
          ]">
            <div class="flex items-center gap-2 cursor-pointer font-bold text-primary collapse-title"
              @click="scrollToSection(index)">
              <input type="checkbox" class="checkbox checkbox-xs checkbox-primary cursor-default"
                :checked="filledStatus[step.id]?.groupFilled" onclick="return false;" />
              {{ currentLocale === "de" ? step.de : step.en }}
            </div>

            <ul v-if="requiredTree[step.id]" class="ml-4 mt-1 border-l border-base-300 pl-2 collapse-content">
              <li v-for="(levelData, firstLevel) in requiredTree[step.id]" :key="firstLevel" class="mb-1">
                <div class="flex items-center gap-2 cursor-default text-sm font-semibold opacity-80"
                  @click="scrollToSection(index)">
                  <input type="checkbox" class="checkbox checkbox-xs cursor-default"
                    :checked="filledStatus[step.id]?.firstLevels?.[firstLevel]" onclick="return false;" />
                  {{
                    currentLocale === "de" ? levelData.nameDE : levelData.nameEN
                  }}
                </div>

                <!--     <ul class="ml-4 mt-1 border-l border-base-300 pl-2">
                  <li v-for="field in fields" :key="field.fieldPath">
                    <div class="flex items-center gap-2 cursor-pointer text-xs opacity-70" @click="scrollToSection(index)">
                      <input 
                        type="checkbox" 
                        class="checkbox checkbox-xs" 
                        :checked="isFieldFilled(step.id, field.fieldPath)" 
                        readonly
                      />
                      {{ currentLocale === 'de' ? (field.nameDE || field.fieldPath.split('.').pop()) : (field.nameEN || field.fieldPath.split('.').pop()) }}
                    </div>
                  </li>
                </ul>-->
              </li>
            </ul>
          </li>
        </ul>

        <div class="flex flex-row items-center justify-start text-left w-full pt-2">
          <label class="label">
            <span class="text-accent font-black w-4">*</span>{{ t("req") }}</label>
        </div>
        <div class="flex flex-row items-center w-full pb-2">
          <label class="label">
            <span class="text-warning font-black w-4">~</span>{{ t("rec") }}</label>
        </div>
        <input type="file" ref="fileInput" class="hidden" accept=".zip" @change="importFromBagIt" />

        <button class="w-full btn btn-outline m-2 hidden" @click="triggerImport">
          {{ t("importBagIt") }}
          <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px"
            fill="currentColor" class="size-[1.2em] primary-content">
            <path
              d="M440-320v-326L336-542l-56-58 200-200 200 200-56 58-104-104v326h-80ZM240-160q-33 0-56.5-23.5T160-240v-120h80v120h480v-120h80v120q0 33-23.5 56.5T720-160H240Z" />
          </svg>
        </button>

        <button class="w-full btn btn-outline m-2" @click="exportToBagInfoTxt"
          :disabled="currentFile != '' && progress != 100">
          {{ t("exportBagIt") }}
          <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px"
            fill="currentColor" stroke-width="2.5" stroke="currentColor" class="size-[1.2em] primary-content">
            <path d="M160-80v-80h640v80H160Zm320-160L200-600h160v-280h240v280h160L480-240Z" />
          </svg>
        </button>
        <!-- <div v-if="exportStatus === 'success'"
          class="p-3 font-bold text-green-800 bg-green-100 border border-green-300 rounded-md">
          ✅ BagIt export downloaded successfully!
        </div>

        <div v-if="exportStatus === 'error'"
          class="p-3 font-bold text-red-800 bg-red-100 border border-red-300 rounded-md">
          ❌ Failed to export: {{ errorMessage }}
        </div> -->
        <button class="w-full btn btn-outline m-2" @click="exportToCarareXML"
          :disabled="currentFile != '' && progress != 100">
          {{ t("exportCARAREXML") }}
          <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px"
            fill="currentColor" stroke-width="2.5" stroke="currentColor" class="size-[1.2em] primary-content">
            <path d="M160-80v-80h640v80H160Zm320-160L200-600h160v-280h240v280h160L480-240Z" />
          </svg>
        </button>
        <div v-if="exportStatus === 'success'"
          class="p-3 font-bold text-green-800 bg-green-100 border border-green-300 rounded-md">
          ✅ {{ t("exportSuccess") }}
        </div>

        <div v-if="exportStatus === 'error'"
          class="p-3 font-bold text-red-800 bg-red-100 border border-red-300 rounded-md">
          ❌ {{ t("exportError") }} {{ errorMessage }}
        </div>
      </div>

      <div class="w-2/3 flex flex-col gap-12 pb-32" ref="scrollContainer" @scroll="handleScroll">
        <section v-for="(step, index) in steps" :key="index" ref="sectionRefs" :data-index="index"
          class="card bg-base-100 shadow-xl border border-base-200">
          <div class="card-body flex flex-col">
            <div class="relative bg-base-100/95 backdrop-blur sticky top-18 z-40 p-4 mb-6">
              <h2 class="card-title text-2xl border-b pb-2 w-full pr-12">
                {{ currentLocale == "de" ? step.de : step.en }}
              </h2>

              <div class="tooltip tooltip-bottom absolute right-11 top-4" :data-tip="t('addtemplate')"
                v-if="step.id != 'files'">
                <button class="btn btn-square btn-ghost btn-transparent" @click="openAddTemplateModal(step.id)">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                    <path
                      d="M5 21v-16q0 -0.825 0.588 -1.413T7 3h6v2H7v12.95l5 -2.15 5 2.15v-6.95h2v10L12 18 5 21Zm2 -16h6 -6Zm10 4v-2h-2v-2h2v-2h2v2h2v2h-2v2h-2Z" />
                  </svg>
                </button>
              </div>
              <div class="tooltip tooltip-bottom absolute right-1 top-4" :data-tip="t('template')"
                v-if="step.id != 'files'">
                <button class="btn btn-square btn-ghost btn-transparent" @click="openTemplateModal(step.id)">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-6 h-6">
                    <path
                      d="M4 22v-14q0 -0.825 0.588 -1.413T6 6h8q0.825 0 1.413 0.588T16 8v14L10 19 4 22Zm2 -3.025 4 -2.15 4 2.15v-10.975H6v10.975Zm12 -0.975v-14H7v-2h11q0.825 0 1.413 0.588T20 4v14h-2ZM6 8h8 -8Z" />
                  </svg>
                </button>
              </div>
            </div>

            <CollectionInformationCrud v-if="step.id === 'collectionInformation'" ref="collectionRef" />
            <HeritageAssetIdentificationCrud v-if="step.id === 'heritageAssetIdentification'" ref="heritageRef" />
            <DigitalResourceCrud v-if="step.id === 'digitalResource'" ref="digitalRef" />
            <ActivityCrud v-if="step.id === 'activity'" ref="activityRef" />
            <div v-if="step.id === 'files'" ref="fileInput">
              <button class="btn btn-primary" @click="handleDirectorySelect">
                {{ t("selectDir") }}
              </button>

              <div v-if="currentFile && progress != 100" class="mt-4">
                <p class="text-sm truncate">
                  {{ t("hashing") }} {{ currentFile }}
                </p>
                <progress class="progress progress-primary w-full" :class="progress == 100 ? 'progress-green' : 'progress-primary'
                  " :value="progress" max="100"></progress>
                <span class="text-xs">{{ progress.toFixed(2) }}%</span>
              </div>
              <div class="col-span-full border-2 border-base-200 bg-base-100 p-4 rounded-box mb-6 mt-6 shadow-sm">
                <div class="overflow-x-auto">
                  <table class="table table-xs w-full table-zebra">
                    <thead class="bg-base-200">
                      <tr>
                        <th>{{ t("file") }}</th>
                        <th>
                          {{ t("hash") }}
                        </th>
                        <th class="w-24">{{ t("actions") }}</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(hash, fileName, idx) in fileHashes" :key="idx">
                        <td>
                          {{ fileName }}
                        </td>
                        <td>
                          {{ hash }}
                        </td>
                        <td>
                          <div class="flex gap-1">
                            <button type="button" class="btn btn-xs btn-ghost text-info" @click.prevent="edit(idx)">
                              {{ t("edit") }}
                            </button>
                            <button type="button" class="btn btn-xs btn-ghost text-error" @click.prevent="remove(idx)">
                              {{ t("del") }}
                            </button>
                          </div>
                        </td>
                      </tr>
                      <tr v-if="
                        !fileHashes || Object.keys(fileHashes).length === 0
                      ">
                        <td colspan="100" class="text-center text-base-content/40 py-4 italic">
                          {{ t("noItems") }}
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          </div>
        </section>
      </div>
    </div>
  </main>
  <dialog ref="templateModal" class="modal">
    <div class="modal-box max-w-md">
      <h3 class="font-bold text-lg mb-6">{{ currentLocale === 'de' ? 'Vorlage wählen' : 'Choose a Template' }}</h3>

      <form method="dialog" class="flex flex-col gap-3">
        <div v-for="(tpl, idx) in templates[activeTemplateStep] || []" :key="idx" class="join w-full">
          <button class="btn btn-outline justify-start font-normal flex-1 join-item"
            @click="applyTemplate(activeTemplateStep, tpl.data)">
            {{ tpl.name }}
          </button>
          <button class="btn btn-outline btn-error join-item"
            @click.prevent="confirmDeleteTemplate(activeTemplateStep, idx)">
            <svg class="size-5" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor"
              stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round"
                d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
          </button>
        </div>
        <div v-if="!(templates[activeTemplateStep] && templates[activeTemplateStep].length > 0)"
          class="text-sm italic opacity-50 py-4">{{ t('noItems') }}</div>
      </form>

      <div class="divider my-6">{{ t('or') }}</div>

      <div class="modal-action w-full mt-0">
        <form method="dialog" class="w-full">
          <button class="btn btn-primary w-full">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5 mr-1">
              <path
                d="M2.695 14.763l-1.262 3.154a.5.5 0 00.65.65l3.155-1.262a4 4 0 001.343-.885L17.5 5.5a2.121 2.121 0 00-3-3L3.58 13.42a4 4 0 00-.885 1.343z" />
            </svg>
            {{ t('customEdit') }}
          </button>
        </form>
      </div>
    </div>
    <form method="dialog" class="modal-backdrop">
      <button>close</button>
    </form>
  </dialog>
  <dialog ref="addTemplateModal" class="modal">
    <div class="modal-box max-w-md">
      <h3 class="font-bold text-lg mb-6">{{ t('addtemplate') }}</h3>

      <div class="flex flex-col gap-4">
        <input type="text" v-model="newTemplateName" :placeholder="t('templateName')"
          class="input input-bordered w-full" />
        <div class="modal-action mt-2 w-full">
          <form method="dialog" class="w-full">
            <button class="btn btn-primary w-full" @click="addTemplate(activeAddTemplateStep)"
              :disabled="!newTemplateName">
              {{ t('addtemplate') }}
            </button>
          </form>
        </div>
      </div>
    </div>

    <form method="dialog" class="modal-backdrop">
      <button>close</button>
    </form>
  </dialog>
  <dialog ref="deleteTemplateModal" class="modal">
    <div class="modal-box">
      <h3 class="font-bold text-lg">{{ t('deleteTemplatePrompt') }}</h3>
      <p class="py-4">{{ t('deleteTemplateDesc') }}</p>
      <div class="modal-action">
        <form method="dialog" class="flex gap-2">
          <!-- if there is a button in form, it will close the modal -->
          <button class="btn btn-ghost">{{ t('cancel') }}</button>
          <button class="btn btn-error" @click="executeDeleteTemplate">{{ t('del') }}</button>
        </form>
      </div>
    </div>
    <form method="dialog" class="modal-backdrop">
      <button>close</button>
    </form>
  </dialog>
</template>

<script setup>
import JSZip from "jszip";
import { ref, shallowRef, onMounted, onUnmounted, provide, computed } from "vue";
import { themeChange } from "theme-change";
import CollectionInformationCrud from "./output_components/CollectionInformationCrud.vue";
import HeritageAssetIdentificationCrud from "./output_components/HeritageAssetIdentificationCrud.vue";
import DigitalResourceCrud from "./output_components/DigitalResourceCrud.vue";
import ActivityCrud from "./output_components/ActivityCrud.vue";
import { invoke } from "@tauri-apps/api/core";
import { listen } from "@tauri-apps/api/event";
import { open, save } from "@tauri-apps/plugin-dialog";
import { writeFile } from "@tauri-apps/plugin-fs";
import { load } from "@tauri-apps/plugin-store";

const isExporting = ref(false);
const exportStatus = ref(null); // 'success', 'error', or null
const errorMessage = ref("");
const progress = ref(0);
const currentFile = ref("");
const fileHashes = ref({});
let unlistenFn = null;
const collectionTemplateModal = ref(null);
const heritageAssetTemplateModal = ref(null);
const digitalResourceTemplateModal = ref(null);
const activityTemplateModal = ref(null);
const addTemplateModal = ref(null);
const deleteTemplateModal = ref(null);
const templateToDelete = ref(null);
const activeAddTemplateStep = ref(null);
const templateModal = ref(null);
const activeTemplateStep = ref(null);
const templates = ref({});
const newTemplateName = ref("");
const store = shallowRef(null);
const collectionRef = ref([]);
const heritageRef = ref([]);
const digitalRef = ref([]);
const activityRef = ref([]);
const fileInput = ref(null);

// Localization State & Dictionary
const currentLocale = ref("en");
provide("currentLocale", currentLocale);

const uiTranslations = {
  en: {
    importBagIt: "Import BagIt Archive",
    importSuccess: "BagIt Archive successfully imported!",
    importError:
      "Failed to parse BagIt archive. Ensure it is a valid HERMES format.",
    exportBagIt: "BagIt Archive",
    exportCARAREXML: "CARARE XML",
    selectDir: "Select Data Directory to Hash",
    hashing: "Hashing:",
    file: "File",
    hash: "Hash",
    edit: "Edit",
    del: "Delete",
    noItems: "No items added yet.",
    req: "Required fields",
    rec: "Recommended fields",
    template: "Fill from template",
    addtemplate: "Add as template",
    templateName: "Template name",
    or: "OR",
    customEdit: "Custom Edit",
    deleteTemplatePrompt: "Are you sure you want to delete this template?",
    deleteTemplateDesc: "This action cannot be undone.",
    cancel: "Cancel",
    exportSuccess: "Export downloaded successfully!",
    exportError: "Failed to export:",
  },
  de: {
    importBagIt: "BagIt-Archiv importieren",
    importSuccess: "BagIt-Archiv erfolgreich importiert!",
    importError:
      "Fehler beim Parsen des BagIt-Archivs. Stellen Sie sicher, dass es ein gültiges HERMES-Format ist.",
    exportBagIt: "BagIt-Archiv",
    exportCARAREXML: "CARARE XML",
    selectDir: "Datenverzeichnis zum Hashen auswählen",
    hashing: "Wird gehasht:",
    file: "Datei",
    hash: "Hash",
    edit: "Bearbeiten",
    del: "Löschen",
    noItems: "Noch keine Elemente hinzugefügt.",
    req: "Pflichtfelder",
    rec: "Empfohlene Felder",
    template: "Aus Vorlage befüllen",
    addtemplate: "Als Vorlage hinzufügen",
    templateName: "Vorlagenname",
    or: "ODER",
    customEdit: "Manuell bearbeiten",
    deleteTemplatePrompt: "Sind Sie sicher, dass Sie diese Vorlage löschen möchten?",
    deleteTemplateDesc: "Diese Aktion kann nicht rückgängig gemacht werden.",
    cancel: "Abbrechen",
    exportSuccess: "Export erfolgreich heruntergeladen!",
    exportError: "Export fehlgeschlagen:",
  },
};

const t = (key) =>
  uiTranslations[currentLocale.value]?.[key] || uiTranslations.en[key];

const openTemplateModal = (stepId) => {
  activeTemplateStep.value = stepId;
  templateModal.value?.showModal();
};

const openAddTemplateModal = (stepId) => {
  activeAddTemplateStep.value = stepId;
  addTemplateModal.value?.showModal();
};

const addTemplate = async (stepId) => {
  if (!newTemplateName.value || !store.value) return;

  let currentForm = null;
  if (stepId === "collectionInformation") currentForm = collectionRef.value?.[0].form;
  else if (stepId === "heritageAssetIdentification") currentForm = heritageRef.value?.[0].form;
  else if (stepId === "digitalResource") currentForm = digitalRef.value?.[0].form;
  else if (stepId === "activity") currentForm = activityRef.value?.[0].form;

  // console.log(collectionRef.value)
  // console.log(currentForm)
  if (currentForm) {
    const dataToSave = JSON.parse(JSON.stringify(currentForm));
    // console.log(dataToSave)
    if (!templates.value[stepId]) templates.value[stepId] = [];
    templates.value[stepId].push({
      name: newTemplateName.value,
      data: dataToSave
    });

    await store.value.set(stepId, templates.value[stepId]);
    await store.value.save();
  }

  newTemplateName.value = "";
  addTemplateModal.value?.close();
};

const applyTemplate = (stepId, templateData) => {
  let targetRef = null;
  if (stepId === "collectionInformation") targetRef = collectionRef.value;
  else if (stepId === "heritageAssetIdentification") targetRef = heritageRef.value;
  else if (stepId === "digitalResource") targetRef = digitalRef.value;
  else if (stepId === "activity") targetRef = activityRef.value;

  if (targetRef) {
    Object.assign(targetRef[0].form, JSON.parse(JSON.stringify(templateData)));
  }
};

const confirmDeleteTemplate = (stepId, index) => {
  templateToDelete.value = { stepId, index };
  deleteTemplateModal.value?.showModal();
};

const executeDeleteTemplate = async () => {
  if (templateToDelete.value) {
    const { stepId, index } = templateToDelete.value;
    templates.value[stepId].splice(index, 1);
    await store.value.set(stepId, templates.value[stepId]);
    await store.value.save();
    templateToDelete.value = null;
  }
};

onMounted(async () => {
  try {
    store.value = await load('templates.json', { autoSave: true });

    // Load existing templates
    const keys = await store.value.keys();
    for (const stepId of keys) {
      templates.value[stepId] = await store.value.get(stepId) || [];
    }
  } catch (err) {
    console.error("Error loading templates store:", err);
  }

  // Listen for the progress event emitted from Rust
  unlistenFn = await listen("hash-progress", (event) => {
    const { filename, bytes_processed, total_bytes } = event.payload;
    const percentage = (bytes_processed / total_bytes) * 100;

    currentFile.value = filename;
    progress.value = percentage;
  });
});

onUnmounted(() => {
  // Clean up the listener when the component is destroyed
  if (unlistenFn) {
    unlistenFn();
  }
});

let manifestContent = "";
const handleDirectorySelect = async () => {
  try {
    // Open the native OS folder picker
    const selectedDirPath = await open({
      directory: true,
      multiple: false,
    });

    // If the user closes the dialog without selecting anything, just return
    if (!selectedDirPath) return;

    console.log("Selected directory:", selectedDirPath);

    // Pass the clean, absolute path directly to Rust
    fileHashes.value = await invoke("generate_bag_hashes", {
      baseDir: selectedDirPath,
    });

    // Reset manifest content just in case they click it twice
    manifestContent = "";

    for (const [relativePath, hash] of Object.entries(fileHashes.value)) {
      manifestContent += `${hash} data/${relativePath}\n`;
    }
    // console.log(fileHashes.value);

    console.log("Generated Manifest Data:\n", manifestContent);
  } catch (error) {
    // Now, if anything fails (Rust error or JS error), you will actually see it!
    console.error("Error generating hashes:", error);
  }
};

// const stepsEN = [
//   "Collection",
//   "Heritage Asset",
//   "Digital Resources",
//   "Activities",
//   "Files",
// ];

// const stepsDE = [
//   "Sammlung",
//   "Kulturgut",
//   "Digitale Ressourcen",
//   "Aktivitäten",
//   "Dateien",
// ];
import schemaDict from "./assets/schema_dictionary.json";

// Group definition mapping internal IDs to display names
const steps = [
  { id: "collectionInformation", en: "Collection", de: "Sammlung" },
  { id: "heritageAssetIdentification", en: "Heritage Asset", de: "Kulturgut" },
  { id: "digitalResource", en: "Digital Resources", de: "Digitale Ressourcen" },
  { id: "activity", en: "Activities", de: "Aktivitäten" },
  { id: "files", en: "Files", de: "Dateien" },
];

// Tree representation of required fields
const requiredTree = computed(() => {
  const tree = {};
  for (const [group, fields] of Object.entries(schemaDict)) {
    // Only map required fields
    const reqFields = fields.filter((f) => f.status === "required");
    if (reqFields.length === 0) continue;

    tree[group] = {};
    reqFields.forEach((f) => {
      const parts = f.fieldPath.split(".");
      const firstLevel = parts[0];

      if (!tree[group][firstLevel]) {
        // Look up the parent node in the unfiltered fields array
        const parentNode =
          fields.find((field) => field.fieldPath === firstLevel) || {};

        tree[group][firstLevel] = {
          nameEN: parentNode.nameEN || firstLevel,
          nameDE: parentNode.nameDE || firstLevel,
          fields: [], // array to hold the required leaf fields
        };
      }
      tree[group][firstLevel].fields.push(f);
    });
  }
  return tree;
});
const getComponentRef = (groupId) => {
  if (groupId === "collectionInformation") return collectionRef.value?.[0];
  if (groupId === "heritageAssetIdentification") return heritageRef.value?.[0];
  if (groupId === "digitalResource") return digitalRef.value?.[0];
  if (groupId === "activity") return activityRef.value?.[0];
  return null;
};
const isAppMounted = ref(false);
onMounted(() => {
  isAppMounted.value = true;
});

const filledStatus = computed(() => {
  const status = {};
  if (!isAppMounted.value) return status;

  for (const step of steps) {
    const groupId = step.id;
    status[groupId] = { groupFilled: false, firstLevels: {} };

    if (groupId === "files") {
      status[groupId].groupFilled =
        Object.keys(fileHashes.value || {}).length > 0;
      continue;
    }

    const comp = getComponentRef(groupId);
    const fieldsMap = requiredTree.value[groupId];
    console.log(fieldsMap)
    if (!fieldsMap) {
      status[groupId].groupFilled = true;
      continue;
    }

    if (!comp) {
      status[groupId].groupFilled = false;
      continue;
    }

    let allGroupFilled = true;
    for (const [firstLevel, levelData] of Object.entries(fieldsMap)) {
      let allFirstLevelFilled = false;

      const checkFilled = (dataObj) => {
        if (!dataObj) return false;

        const firstLevelVal = dataObj[firstLevel];
        if (Array.isArray(firstLevelVal)) {
          return firstLevelVal.length > 0;
        }

        return levelData.fields.every((field) => {
          const val = dataObj[field.fieldPath];
          if (Array.isArray(val)) return val.length > 0;
          return val !== null && val !== undefined && val !== "";
        });
      };

      // 1. Check if ANY saved item has ALL required fields filled
      if (comp.items && comp.items.length > 0) {
        allFirstLevelFilled = comp.items.some(checkFilled);
      }

      // 2. Fallback to check if the current active form has ALL required fields filled
      if (!allFirstLevelFilled && comp.form) {
        allFirstLevelFilled = checkFilled(comp.form);
      }
      status[groupId].firstLevels[firstLevel] = allFirstLevelFilled;
      if (!allFirstLevelFilled) {
        allGroupFilled = false;
      }
    }
    status[groupId].groupFilled = allGroupFilled;
  }
  return status;
});
const activeStepIndex = ref(0);
const sectionRefs = ref([]);
const scrollContainer = ref(null);
let observer = null;

// Helper to generate a SHA-256 hash from a string
const generateSHA256 = async (text) => {
  const encoder = new TextEncoder();
  const data = encoder.encode(text);
  const hashBuffer = await crypto.subtle.digest("SHA-256", data);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map((b) => b.toString(16).padStart(2, "0")).join("");
};

const scrollToSection = (index) => {
  if (sectionRefs.value[index]) {
    sectionRefs.value[index].scrollIntoView({
      behavior: "smooth",
      block: "start",
    });
  }
};

const handleScroll = () => {
  if (!scrollContainer.value) return;

  const { scrollTop, scrollHeight, clientHeight } = scrollContainer.value;

  // If the user has scrolled to within 5px of the absolute bottom
  if (Math.ceil(scrollTop + clientHeight) >= scrollHeight - 5) {
    activeStepIndex.value = steps.length - 1;
  }
};

onMounted(() => {
  themeChange(false);
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        // If the section intersects with our viewport boundaries, update the active step
        if (entry.isIntersecting) {
          activeStepIndex.value = Number(entry.target.dataset.index);
        }
      });
    },
    {
      // These margins ensure the step updates when the header is near the top of the screen,
      // rather than waiting for the entire section to be visible.
      rootMargin: "-20% 0px -70% 0px",
      threshold: 0,
    },
  );

  sectionRefs.value.forEach((el) => {
    if (el) observer.observe(el);
  });
});

onUnmounted(() => {
  if (observer) observer.disconnect();
});

// 1. Add these refs near your other state variables in App.vue

// ==========================================
// IMPORT LOGIC
// ==========================================
const triggerImport = () => {
  if (fileInput.value) fileInput.value.click();
};

const importFromBagIt = async (event) => {
  const file = event.target.files[0];
  if (!file) return;

  try {
    const zip = await JSZip.loadAsync(file);
    const bagInfoFile = Object.values(zip.files).find((f) =>
      f.name.endsWith("bag-info.txt"),
    );

    if (!bagInfoFile) {
      alert(t("importError"));
      return;
    }

    const bagInfoContent = await bagInfoFile.async("string");
    const lines = bagInfoContent.split("\n");

    const parsedData = {
      Collection: [],
      HeritageAsset: [],
      DigitalResource: [],
      Activity: [],
    };
    let currentRecords = {
      Collection: {},
      HeritageAsset: {},
      DigitalResource: {},
      Activity: {},
    };
    let firstKeyOfSection = {
      Collection: null,
      HeritageAsset: null,
      DigitalResource: null,
      Activity: null,
    };

    const setNestedValue = (obj, pathParts, value) => {
      let current = obj;
      for (let i = 0; i < pathParts.length - 1; i++) {
        if (!current[pathParts[i]]) current[pathParts[i]] = {};
        current = current[pathParts[i]];
      }
      const lastKey = pathParts[pathParts.length - 1];

      // Handle properties that were converted into arrays of primitives (e.g., tags)
      if (current[lastKey] !== undefined) {
        if (!Array.isArray(current[lastKey]))
          current[lastKey] = [current[lastKey]];
        current[lastKey].push(value);
      } else {
        current[lastKey] = value;
      }
    };

    const stdHeaders = [
      "Bagging-Date",
      "BagIt-Version",
      "Tag-File-Character-Encoding",
      "Payload-Oxum",
      "Bag-Size",
    ];

    lines.forEach((line) => {
      const separatorIdx = line.indexOf(":");
      if (separatorIdx === -1) return;

      const rawKey = line.substring(0, separatorIdx).trim();
      const value = line.substring(separatorIdx + 1).trim();

      if (stdHeaders.includes(rawKey) || !value) return;

      const parts = rawKey.split(".");
      const section = parts.shift(); // E.g., 'Activity'

      if (!parsedData[section]) return;

      const rootField = parts[0];

      // Heuristic: If we hit the same starting key again, it means a new record block has started
      if (!firstKeyOfSection[section]) {
        firstKeyOfSection[section] = rootField;
      } else if (rootField === firstKeyOfSection[section]) {
        parsedData[section].push({ ...currentRecords[section] });
        currentRecords[section] = {};
      }

      setNestedValue(currentRecords[section], parts, value);
    });

    // Push the final record from the loop
    Object.keys(currentRecords).forEach((sec) => {
      if (Object.keys(currentRecords[sec]).length > 0) {
        parsedData[sec].push(currentRecords[sec]);
      }
    });

    // Feed Data directly back into child Crud components
    if (collectionRef.value?.[0])
      collectionRef.value[0].items = parsedData.Collection;
    if (heritageRef.value?.[0])
      heritageRef.value[0].items = parsedData.HeritageAsset;
    if (digitalRef.value?.[0])
      digitalRef.value[0].items = parsedData.DigitalResource;
    if (activityRef.value?.[0])
      activityRef.value[0].items = parsedData.Activity;

    alert(t("importSuccess"));
  } catch (error) {
    console.error(error);
    alert(t("importError"));
  }

  // Clear file input to allow re-importing the same file
  event.target.value = "";
};

// ==========================================
// EXPORT LOGIC
// ==========================================
const getRecordsFromComponent = (componentRef) => {
  const child = componentRef.value?.[0];
  if (!child) return [];

  const records = [...(child.items || [])];

  if (child.form) {
    const hasUnsavedData = Object.values(child.form).some((val) => {
      if (Array.isArray(val)) return val.length > 0;
      return val !== "" && val !== null && val !== undefined;
    });

    if (hasUnsavedData) {
      records.push(JSON.parse(JSON.stringify(child.form)));
    }
  }

  const cleanEmptyAttributes = (obj) => {
    const isEmptyVal = (val) => {
      if (val === "" || val === null || val === undefined) return true;
      if (Array.isArray(val) && val.length === 0) return true;
      return false;
    };

    if (Array.isArray(obj)) {
      return obj.map((item) => cleanEmptyAttributes(item));
    } else if (obj && typeof obj === "object") {
      const cleaned = {};
      for (const [k, v] of Object.entries(obj)) {
        if (k.includes("#")) {
          const parentKey = k.split("#")[0];
          let hasValue = false;

          if (obj.hasOwnProperty(parentKey)) {
            hasValue = !isEmptyVal(obj[parentKey]);
          } else if (
            obj.hasOwnProperty("_value") ||
            obj.hasOwnProperty("value") ||
            obj.hasOwnProperty("#text")
          ) {
            let parentVal = obj["_value"];
            if (parentVal === undefined) parentVal = obj["value"];
            if (parentVal === undefined) parentVal = obj["#text"];
            hasValue = !isEmptyVal(parentVal);
          }

          if (!hasValue) {
            continue;
          }
        }
        cleaned[k] = typeof v === "object" && v !== null ? cleanEmptyAttributes(v) : v;
      }
      return cleaned;
    }
    return obj;
  };

  return records.map(record => cleanEmptyAttributes(record));
};

const exportToBagInfoTxt = async () => {
  // Reset states and indicate loading
  isExporting.value = true;
  exportStatus.value = null;
  errorMessage.value = "";

  try {
    const allData = {
      Collection: getRecordsFromComponent(collectionRef),
      HeritageAsset: getRecordsFromComponent(heritageRef),
      DigitalResource: getRecordsFromComponent(digitalRef),
      Activity: getRecordsFromComponent(activityRef),
    };

    let bagInfoContent = "";
    bagInfoContent += `Bagging-Date: ${new Date().toISOString().substring(0, 10)}\n`;

    const processField = (path, value) => {
      if (value === null || value === undefined || value === "") return;

      if (Array.isArray(value)) {
        if (value.length === 0) return;
        value.forEach((item) => {
          if (typeof item === "object" && item !== null) {
            for (const [subKey, subValue] of Object.entries(item)) {
              processField(`${path}.${subKey}`, subValue);
            }
          } else {
            processField(path, item);
          }
        });
      } else if (typeof value === "object") {
        for (const [subKey, subValue] of Object.entries(value)) {
          processField(`${path}.${subKey}`, subValue);
        }
      } else {
        const cleanValue = String(value)
          .replace(/[\r\n]+/g, " ")
          .trim();
        bagInfoContent += `${path}: ${cleanValue}\n`;
      }
    };

    for (const [sectionTitle, records] of Object.entries(allData)) {
      records.forEach((record) => {
        for (const [key, value] of Object.entries(record)) {
          processField(`${sectionTitle}.${key}`, value);
        }
      });
    }

    let bagitContent = `BagIt-Version: 1.0\n`;
    bagitContent += `Tag-File-Character-Encoding: UTF-8\n`;

    const bagitHash = await generateSHA256(bagitContent);
    const bagInfoHash = await generateSHA256(bagInfoContent);

    let tagmanifestContent = `${bagitHash} bagit.txt\n`;
    tagmanifestContent += `${bagInfoHash} bag-info.txt\n`;

    const zip = new JSZip();

    zip.file("bagit.txt", bagitContent);
    zip.file("bag-info.txt", bagInfoContent);
    zip.file("tagmanifest-sha256.txt", tagmanifestContent);
    zip.file("manifest-sha256.txt", manifestContent);

    zip.folder("data");

    const zipArrayBuffer = await zip.generateAsync({ type: "uint8array" });

    // --- 2. Open Native 'Save As' Dialog ---
    const defaultFilename = `hermes-bagit-${Date.now()}.zip`;
    const selectedPath = await save({
      defaultPath: defaultFilename,
      filters: [
        {
          name: "ZIP Archive",
          extensions: ["zip"],
        },
      ],
    });

    // --- 3. Handle Dialog Cancellation ---
    if (!selectedPath) {
      // User clicked "Cancel" on the save dialog.
      isExporting.value = false;
      return;
    }

    // --- 4. Write the binary array directly to the chosen system path ---
    await writeFile(selectedPath, zipArrayBuffer);

    // --- On Successful Save ---
    exportStatus.value = "success";

    setTimeout(() => {
      exportStatus.value = null;
    }, 4000);
  } catch (error) {
    console.error("Export failed:", error);
    exportStatus.value = "error";
    errorMessage.value =
      error.message || "An unexpected error occurred during export.";
  } finally {
    isExporting.value = false;
  }
};

const exportToCarareXML = async () => {
  // Reset states and indicate loading
  isExporting.value = true;
  exportStatus.value = null;
  errorMessage.value = "";

  try {
    const allData = {
      Collection: getRecordsFromComponent(collectionRef),
      HeritageAsset: getRecordsFromComponent(heritageRef),
      DigitalResource: getRecordsFromComponent(digitalRef),
      Activity: getRecordsFromComponent(activityRef),
    };

    class XmlNode {
      constructor(name) {
        this.name = name;
        this.attributes = {};
        this.text = "";
        this.children = [];
      }

      getOrCreateChild(name) {
        let child = this.children.find((c) => c.name === name);
        if (!child) {
          child = new XmlNode(name);
          this.children.push(child);
        }
        return child;
      }

      addChild(name) {
        const child = new XmlNode(name);
        this.children.push(child);
        return child;
      }
    }

    const sectionTagMap = {
      Collection: "CollectionInformation",
      HeritageAsset: "HeritageAssetIdentification",
      DigitalResource: "DigitalResource",
      Activity: "Activity",
    };

    const navigateSegments = (node, segments) => {
      let current = node;
      let startIndex = 0;
      if (
        segments.length > 0 &&
        segments[0].toLowerCase() === current.name.toLowerCase()
      ) {
        startIndex = 1;
      }
      for (let i = startIndex; i < segments.length; i++) {
        current = current.getOrCreateChild(segments[i]);
      }
      return current;
    };

    const populateNode = (targetNode, data) => {
      if (data === null || data === undefined) return;

      if (typeof data !== "object") {
        targetNode.text = String(data);
        return;
      }

      for (const [rawKey, val] of Object.entries(data)) {
        if (val === null || val === undefined || val === "") continue;

        if (rawKey.startsWith("#")) {
          const attrName = rawKey.substring(1);
          targetNode.attributes[attrName] = String(val);
          continue;
        }

        if (rawKey === "_value" || rawKey === "value" || rawKey === "#text") {
          targetNode.text = String(val);
          continue;
        }

        let fieldPath = rawKey;
        let attrName = null;
        if (rawKey.includes("#")) {
          const parts = rawKey.split("#");
          fieldPath = parts[0];
          attrName = parts[1];
        }

        const segments = fieldPath
          .split(".")
          .map((s) => s.charAt(0).toUpperCase() + s.slice(1))
          .filter(Boolean);

        if (segments.length === 0) {
          if (attrName) {
            targetNode.attributes[attrName] = String(val);
          }
          continue;
        }

        const lastSegment = segments[segments.length - 1];

        if (Array.isArray(val)) {
          if (val.length === 0) continue;

          const parentSegments = segments.slice(0, segments.length - 1);
          const parent = navigateSegments(targetNode, parentSegments);

          val.forEach((item) => {
            if (item === null || item === undefined || item === "") return;
            const child = parent.addChild(lastSegment);
            if (attrName && typeof item !== "object") {
              child.attributes[attrName] = String(val);
            }
            populateNode(child, item);
          });
        } else {
          if (attrName && lastSegment.toLowerCase() === targetNode.name.toLowerCase()) {
            targetNode.attributes[attrName] = String(val);
          } else {
            const current = navigateSegments(targetNode, segments);
            if (attrName) {
              current.attributes[attrName] = String(val);
            } else {
              populateNode(current, val);
            }
          }
        }
      }
    };

    const hasContent = (node) => {
      if (
        node.text !== null &&
        node.text !== undefined &&
        String(node.text).trim() !== ""
      ) {
        return true;
      }
      if (Object.keys(node.attributes).length > 0) {
        return true;
      }
      return node.children.some((c) => hasContent(c));
    };

    const escapeXml = (str) => {
      if (str === null || str === undefined) return "";
      return String(str)
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&apos;");
    };

    const serializeXmlNode = (node, indentLevel = 0) => {
      const indent = "  ".repeat(indentLevel);

      let attrStr = "";
      for (const [attrName, attrVal] of Object.entries(node.attributes)) {
        if (attrVal !== null && attrVal !== undefined && attrVal !== "") {
          attrStr += ` ${attrName}="${escapeXml(attrVal)}"`;
        }
      }

      const validChildren = node.children.filter((c) => hasContent(c));
      const hasText =
        node.text !== null &&
        node.text !== undefined &&
        String(node.text).trim() !== "";
      const hasChildren = validChildren.length > 0;

      if (
        !hasText &&
        !hasChildren &&
        Object.keys(node.attributes).length === 0
      ) {
        return "";
      }

      if (hasChildren) {
        let xml = `${indent}<${node.name}${attrStr}>\n`;
        if (hasText) {
          xml += `${indent}  ${escapeXml(node.text)}\n`;
        }
        for (const child of validChildren) {
          const childXml = serializeXmlNode(child, indentLevel + 1);
          if (childXml) {
            xml += childXml;
          }
        }
        xml += `${indent}</${node.name}>\n`;
        return xml;
      } else if (hasText) {
        return `${indent}<${node.name}${attrStr}>${escapeXml(node.text)}</${node.name}>\n`;
      } else {
        return `${indent}<${node.name}${attrStr} />\n`;
      }
    };

    const rootNode = new XmlNode("carareWrap");
    rootNode.attributes["xmlns"] = "http://www.carare.eu/schema/carare/";
    const carareContainer = rootNode.addChild("carare");

    for (const [sectionKey, records] of Object.entries(allData)) {
      const sectionTagName = sectionTagMap[sectionKey] || sectionKey;
      records.forEach((record) => {
        const recordNode = carareContainer.addChild(sectionTagName);
        populateNode(recordNode, record);
      });
    }

    let carareXmlContent = `<?xml version="1.0" encoding="UTF-8"?>\n`;
    carareXmlContent += serializeXmlNode(rootNode, 0);

    const defaultFilename = `hermes-carare-${Date.now()}.xml`;
    const selectedPath = await save({
      defaultPath: defaultFilename,
      filters: [
        {
          name: "CARARE XML File",
          extensions: ["xml"],
        },
      ],
    });

    if (!selectedPath) {
      isExporting.value = false;
      return;
    }

    const encoder = new TextEncoder();
    const xmlArrayBuffer = encoder.encode(carareXmlContent);

    await writeFile(selectedPath, xmlArrayBuffer);

    exportStatus.value = "success";
    setTimeout(() => {
      exportStatus.value = null;
    }, 4000);
  } catch (error) {
    console.error("CARARE XML Export failed:", error);
    exportStatus.value = "error";
    errorMessage.value =
      error.message || "An unexpected error occurred during XML export.";
  } finally {
    isExporting.value = false;
  }
};
</script>
