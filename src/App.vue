
<template>
  <div class="card">
    <Toolbar class="mb-4">
      <template #start>
        <Button
          label="Tambah data pegawai"
          icon="pi pi-plus"
          severity="success"
          class="mr-2"
          @click="toggleModal(true, null)"
        />
      </template>
    </Toolbar>
    <DataTable :value="employees" tableStyle="min-width: 50rem">
      <Column header="Nama Lengkap">
        <template #body="slotProps">
          {{ slotProps.data.first_name }} {{ slotProps.data.last_name }}
        </template>
      </Column>
      <Column header="Jenis Kelamin">
        <template #body="slotProps">
          {{ slotProps.data.gender === "M" ? "Laki-laki" : "Perempuan" }}
        </template>
      </Column>
      <Column field="role" header="Pekerjaan"></Column>
      <Column header="Gaji">
        <template #body="slotProps">
          <span class="font-bold">{{
            `Rp ${slotProps.data.salary.toLocaleString("id-ID")},00`
          }}</span>
        </template>
      </Column>
      <Column header="Tanggal Lahir & Usia">
        <template #body="slotProps">
          <p class="m-0 text-green-700">
            {{ formattedDate(slotProps.data.birth_date) }}
          </p>
          <p class="m-0 font-bold">
            {{ formattedAge(slotProps.data.birth_date) }} tahun
          </p>
        </template>
      </Column>
      <Column header="Tanggal Mulai Bekerja & Periode">
        <template #body="slotProps">
          <p class="m-0 text-green-700">
            {{ formattedDate(slotProps.data.join_date) }}
          </p>
          <p class="m-0 font-bold">
            {{ formattedWorkPeriod(slotProps.data.join_date) }} bulan
          </p>
        </template>
      </Column>
      <Column header="Action">
        <template #body="slotProps">
          <div class="flex">
            <Button
              icon="pi pi-pencil"
              outlined
              rounded
              class="mr-2"
              @click="toggleModal(true, slotProps.data)"
            />
            <Button
              icon="pi pi-trash"
              outlined
              rounded
              severity="danger"
              @click="toggleModal(true, slotProps.data)"
            />
          </div>
        </template>
      </Column>
    </DataTable>

    <!-- dialog insert -->
    <Modal
      :showModalProps="showModal"
      @close="toggleModal"
      @insertUpdate="fetchAllEmployee"
      :employee="employeeData"
      :addMode="addMode"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { format, differenceInYears, differenceInMonths } from "date-fns";
import axios from "axios";

import Modal from "./components/Modal.vue";

const employees = ref([]);
const employeeData = ref({});

onMounted(() => {
  fetchAllEmployee();
});

const fetchAllEmployee = async () => {
  const { data } = await axios.get(import.meta.env.VITE_BASE_URL);
  employees.value = [...data.data];
};

const formattedDate = (paramDate) => {
  return format(new Date(paramDate), "dd MMMM yyyy");
};

const formattedAge = (birthDate) => {
  return differenceInYears(new Date(), new Date(birthDate));
};

const formattedWorkPeriod = (joinDate) => {
  return differenceInMonths(new Date(), new Date(joinDate));
};

const showModal = ref(false);
const addMode = ref(true);

const toggleModal = (value, data) => {
  showModal.value = value;
  if (data) {
    employeeData.value = { ...data };
    addMode.value = false;
  } else {
    addMode.value = true;
  }
};
</script>
