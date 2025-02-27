<script setup lang="ts">
import ReusableForm from '../components/ReusableFormComponent.vue';
import { onMounted, ref } from 'vue';
import CommercialService from '@/services/CommercialService';

const props = defineProps<{
  projectId: string;
  propertyId?: string;
  buildingId?: string;
  commercialId?: string;
  headline: string; // Optional headline text
  saveButtonText: string; // Text for the save button
  cancelButtonText: string; // Text for the cancel button
  submit?: () => void; // Submit handler
  cancel?: () => void; // Cancel handler
}>();

const fields: {
  name: string;
  label: string;
  type: 'text' | 'textarea' | 'checkbox' | 'select';
  options?: any[];
  required?: boolean;
  validations?: ((value: any) => string | null)[];
}[] = [
  { name: 'title', label: 'Titel', type: 'text', required: true },
  { name: 'location', label: 'Standort', type: 'textarea', required: false },
  {
    name: 'commercialSpace',
    label: 'Gewerbefläche (qm)',
    type: 'text',
    validations: [
      (value: any) => (!isNaN(value) ? null : 'Muss eine Zahl sein'),
      (value: any) => (value > 0 ? null : 'Muss größer als 0 sein'),
    ],
  },
  {
    name: 'usableSpace',
    label: 'Nutzfläche (qm)',
    type: 'text',
    validations: [
      (value: any) => (!isNaN(value) ? null : 'Muss eine Zahl sein'),
      (value: any) => (value > 0 ? null : 'Muss größer als 0 sein'),
    ],
  },
  {
    name: 'heatingSpace',
    label: 'Heizfläche (qm)',
    type: 'text',
    validations: [
      (value: any) => (!isNaN(value) ? null : 'Muss eine Zahl sein'),
      (value: any) => (value > 0 ? null : 'Muss größer als 0 sein'),
    ],
  },
  {
    name: 'description',
    label: 'Beschreibung',
    type: 'textarea',
    validations: [
      (value: any) =>
        value.length <= 500 ? null : 'Beschreibung muss 500 Zeichen oder weniger sein',
    ],
  },
];

const initialCommercialData = ref({
  title: '',
  location: '',
  commercialSpace: '',
  usableSpace: '',
  heatingSpace: '',
  description: '',
});

const parentBuildingId = ref(props.buildingId);

onMounted(async () => {
  if (props.commercialId) {
    const projectService = new CommercialService();
    try {
      const response = await projectService.getCommercial(props.projectId, props.commercialId);
      initialCommercialData.value = response;
      if (!parentBuildingId.value) {
        parentBuildingId.value = response.buildingId;
      }
    } catch (error) {
      console.error('Error fetching commercial data:', error);
    }
  }
});
</script>

<template>
  <ReusableForm
    :headline="props.headline"
    :fields="fields"
    :initialValues="initialCommercialData"
    :saveButtonText="props.saveButtonText"
    :cancelButtonText="props.cancelButtonText"
    @submit="props.submit"
    @cancel="props.cancel"
  />
</template>
