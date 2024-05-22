
<template>
  <div class="card">
      <DataTable :value="employees" tableStyle="min-width: 50rem">
          <Column header="Nama Lengkap">
            <template #body="slotProps">
              {{ slotProps.data.first_name }} {{ slotProps.data.last_name }}
            </template>
          </Column>
          <Column header="Jenis Kelamin">
            <template #body="slotProps">
              {{ slotProps.data.gender === 'M' ? 'Laki-laki' : 'Perempuan' }} 
            </template>
          </Column>
          <Column field="role" header="Pekerjaan"></Column>
          <Column header="Gaji">
            <template #body="slotProps">
              <span class="font-bold">{{ `Rp ${slotProps.data.salary.toLocaleString("id-ID")}` }}</span>
            </template>
          </Column>
          <Column header="Tanggal Lahir & Usia">
            <template #body="slotProps">
              <p class="m-0 text-green-700">{{ formattedDate(slotProps.data.birth_date) }}</p>
              <p class="m-0 font-bold">{{ formattedAge(slotProps.data.birth_date) }} tahun</p>
            </template>
          </Column>
          <Column header="Tanggal Mulai Bekerja & Periode">
            <template #body="slotProps">
              <p class="m-0 text-green-700"> {{ formattedDate(slotProps.data.join_date) }}</p>
              <p class="m-0 font-bold">{{ formattedWorkPeriod(slotProps.data.join_date) }} bulan</p>
            </template>
          </Column>
          <Column header="Action"></Column>
      </DataTable>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { format, differenceInYears, differenceInMonths } from 'date-fns'
import axios from 'axios';

const employees = ref([]);

onMounted(async () => {
  const {data} = await axios.get(import.meta.env.VITE_BASE_URL)
  employees.value = [...data.data]
});

const formattedDate = (paramDate) => {
    return format(new Date(paramDate), "dd MMMM yyyy")
}

const formattedAge = (birthDate) => {
    return differenceInYears(new Date(), new Date(birthDate))
}

const formattedWorkPeriod = (joinDate) => {
    return differenceInMonths(new Date(), new Date(joinDate))
}
</script>
