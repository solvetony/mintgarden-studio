<script setup lang="ts">
import { defineProps, ref } from 'vue';
import { PhotographIcon } from '@heroicons/vue/outline';
import { File } from 'nft.storage';
import fs from 'fs';
import path from 'path';

const { modelValue } = defineProps(['modelValue']);
const emit = defineEmits(['update:modelValue']);

const active = ref(false);

const drop = async (e: any) => {
  active.value = false;
  const file = e.dataTransfer.files[0];
  loadFile(file);
};
const selectedFile = async (e) => {
  const file = (document.querySelector('#dropzoneFile') as any).files[0];
  loadFile(file);
  console.log('change', file);
};

const updFile = async (e) => {
  const file = (document.querySelector('#dropJSON') as any).files[0];
  const settings = JSON.parse(fs.readFileSync(file.path, 'utf-8'))
  document.querySelector('#name').value = settings.name;
  document.querySelector('#description').value = settings.description;
  document.querySelector('#royaltyPercentage').value = settings.percentage;
  document.querySelector('#blockchainFee').value = settings.fee;
  document.querySelector('#a-value').value = settings.a;
  document.querySelector('#b-value').value = settings.b;
  document.querySelector('#c-value').value = settings.c;
  document.querySelector('#d-value').value = settings.d; 
};

const loadFile = (file: File) => {
  // TODO ensure that file.size is not too large?

  const blob = fs.readFileSync(file.path);
  //const settings = JSON.parse(fs.readFileSync(path.normalize(path.dirname(file.path) + '/object.json'), 'utf-8'));
  emit('update:modelValue', {
    name: file.name,
    type: file.type,
    content: blob,
    objectUrl: URL.createObjectURL(new Blob([blob])),
  });
  (document.querySelector('#dropzoneFile') as any).value = '';
};
</script>

<template>
  <label
    id="dropzoneFileLabel"
    for="dropzoneFile"
    @dragenter.prevent="active = true"
    @dragleave.prevent="active = false"
    @dragover.prevent
    @drop.prevent="drop"
    :class="[active ? 'border-gray-400' : 'border-gray-300']"
    class="cursor-pointer relative block w-full border-2 border-dashed rounded-lg p-12 text-center hover:border-gray-400 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
  >
    <PhotographIcon
      :class="[active ? 'text-gray-500' : 'text-gray-400']"
      class="pointer-events-none mx-auto h-12 w-12"
    />
    <span class="pointer-events-none mt-2 block text-sm font-medium text-gray-600"
      >Drop an image, or click to select a file</span
    >
    <div class="text-xs text-gray-500">PNG, JPG, GIF, ...</div>
    <input @change="selectedFile" type="file" id="dropzoneFile" class="hidden" accept="image/*" />
  </label>
  <input @change="updFile" type="file" id="dropJSON" class="hidden" accept="json/*" />
</template>
