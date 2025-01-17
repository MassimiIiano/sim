<script setup lang="ts">
import { ref, computed } from 'vue';
import { useI18n } from 'vue-i18n';
import axios from 'axios';

const { t } = useI18n({ useScope: 'global' });

interface Vulnerability {
    id: string;
    title: string;
    matches: string;
    product: string;
    version: string;
    link: string;
  }
interface Device{
  ip: string;
  mac: string;
  device: string;
  vulnerabilities: Vulnerability[];
}
const devices = ref<Device[]>([]);
const fetching = ref(false);

const headers = ref([
  { title: computed(() => t('views.devices.columns.private-ip')), value: 'ip', sortable: true },
  { title: computed(() => t('views.devices.columns.mac-address')), value: 'mac', sortable: true },
  { title: computed(() => t('views.devices.columns.model-name')), value: 'device', sortable: true },
  { title: computed(() => t('views.devices.columns.vulnerabilities')), value: 'vulnerabilities', sortable: false },
]);
const search = ref('');

async function getDevices() {
  const result = await axios.get('http://localhost:5000/devices');
  const data = result.data;
  const devices: Device[] = [];
  Object.keys(data).forEach(element => {
    devices.push({
      ip: data[element].ip,
      mac: data[element].mac,
      device: data[element].device,
      vulnerabilities: data[element].vulnerabilities
    });
  });
  console.log(devices)
  return devices;
}
        
async function fetchData() {
  try {
    fetching.value = true;
    devices.value = await getDevices();
  } finally {
    fetching.value = false;
  }
}

fetchData();
</script>

<template>
  <v-card-text class="mt-4 mx-2">
    <v-data-table
      :loading="fetching"
      :headers="headers"
      :items="devices"
      :search="search"
      multi-sort
      show-expand
    >
      <template #top>
        <v-toolbar color="surface" flat>
          <v-container class="pa-0 fill-height fluid d-flex" fluid>
            <v-toolbar-title class="mr-4">{{ t('views.devices.title') }}</v-toolbar-title>
            <v-text-field
              class="flex"
              v-model="search"
              :label="t('views.devices.search')"
              variant="outlined"
              density="compact"
              clearable
              hide-details
              prepend-inner-icon="mdi-magnify"
            />
          </v-container>
        </v-toolbar>
      </template>

      <template #item.vulnerabilities="{ item }">
        <span :class="{ vulnerable: item.vulnerabilities && item.vulnerabilities.length > 0}">{{ item.vulnerabilities ? item.vulnerabilities.length : 0 }}</span>
      </template>

      <template v-slot:expanded-row="{ columns, item }">
        <tr v-for="vulnerability in item.vulnerabilities" :key="vulnerability.id">
          <td :colspan="columns.length">
                <p class="my-4">Product: {{ vulnerability.product }}</p>
                <p class="my-4" v-if="vulnerability.version">Version: {{ vulnerability.version }}</p>
                <p class="my-4">
                  <a :href="vulnerability.link">{{ vulnerability.title }}</a>
                </p>
          </td>
        </tr>
      </template>
    </v-data-table>
  </v-card-text>
</template>

<style scoped>
.vulnerable {
  color: red;
  font-weight: bold;
}
</style>