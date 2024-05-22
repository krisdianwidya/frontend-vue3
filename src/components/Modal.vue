<template>
  <Toast />
  <Dialog
    v-model:visible="showModal"
    modal
    header="Tambah data pegawai"
    :closable="false"
  >
    <div class="flex align-items-center gap-3 mb-3">
      <label for="firstName" class="font-semibold w-6rem">Nama awal</label>
      <InputText
        id="firstName"
        v-model="firstName"
        class="flex-auto"
        autocomplete="off"
      />
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="lastName" class="font-semibold w-6rem">Nama akhir</label>
      <InputText
        id="lastName"
        v-model="lastName"
        class="flex-auto"
        autocomplete="off"
      />
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="gender" class="font-semibold w-6rem">Jenis kelamin</label>
      <div class="flex flex-wrap gap-3">
        <div class="flex align-items-center">
          <RadioButton v-model="gender" inputId="gender1" value="M" />
          <label for="gender1" class="ml-2">Laki-laki</label>
        </div>
        <div class="flex align-items-center">
          <RadioButton v-model="gender" inputId="gender2" value="F" />
          <label for="gender2" class="ml-2">Perempuan</label>
        </div>
      </div>
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="role" class="font-semibold w-6rem">Pekerjaan</label>
      <Dropdown
        v-model="selectedRole"
        :options="roles"
        optionLabel="name"
        placeholder="Pilih pekerjaan"
        class="w-full md:w-14rem"
      />
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="salary" class="font-semibold w-6rem">Gaji</label>
      <InputNumber
        v-model="salary"
        mode="currency"
        inputId="locale-us"
        locale="id-ID"
        currency="IDR"
      />
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="birthDate" class="font-semibold w-6rem">Tanggal Lahir</label>
      <Calendar v-model="birthDate" />
    </div>
    <div class="flex align-items-center gap-3 mb-3">
      <label for="joinDate" class="font-semibold w-6rem"
        >Tanggal mulai bekerja</label
      >
      <Calendar v-model="joinDate" />
    </div>
    <div class="flex justify-content-end gap-2">
      <Button
        type="button"
        label="Cancel"
        @click="closeModal"
        severity="secondary"
      ></Button>
      <Button type="button" label="Save" @click="insertEmployee"></Button>
    </div>
  </Dialog>

  <Dialog v-model:visible="loading" header="Loading" modal :closable="false">
  </Dialog>
</template>

<script setup>
import { ref, defineProps, watch, defineEmits } from "vue";
import axios from "axios";
import { format } from "date-fns";
import Toast from "primevue/toast";
import { useToast } from "primevue/usetoast";
const toast = useToast();

const showModal = ref(false);
const props = defineProps({
  showModalProps: {
    type: Boolean,
    default: false,
  },
});

watch(
  () => props.showModalProps,
  (newVal) => {
    if (newVal) {
      showModal.value = newVal;
    }
  }
);

const emit = defineEmits(["close", "insertUpdate"]);
const closeModal = () => {
  showModal.value = false;
  emit("close", false);
};

const firstName = ref("");
const lastName = ref("");
const gender = ref("");
const salary = ref(0);
const birthDate = ref(null);
const joinDate = ref(null);

const selectedRole = ref(null);
const roles = ref([
  { name: "Admin" },
  { name: "Kasir" },
  { name: "Staff Gudang" },
  { name: "Koki" },
  { name: "Barista" },
]);

const formattedDate = (date) => {
  return format(date, "yyyy-MM-dd");
};

const resetValue = () => {
  firstName.value = "";
  lastName.value = "";
  gender.value = "";
  selectedRole.value = null;
  salary.value = 0;
  birthDate.value = null;
  joinDate.value = null;
};

const loading = ref(false);
const insertEmployee = async () => {
  loading.value = true;

  const data = {
    first_name: firstName.value,
    last_name: lastName.value,
    gender: gender.value,
    role: selectedRole.value.name,
    salary: salary.value,
    birth_date: formattedDate(birthDate.value),
    join_date: formattedDate(joinDate.value),
  };

  await axios.post(import.meta.env.VITE_BASE_URL, data);
  resetValue();
  loading.value = false;
  closeModal();
  emit("insertUpdate");
  toast.add({
    severity: "success",
    summary: "Berhasil",
    detail: "Data pegawai telah ditambahkan",
    life: 3000,
  });
};
</script>