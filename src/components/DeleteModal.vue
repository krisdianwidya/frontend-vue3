<template>
  <Toast />
  <Dialog
    v-model:visible="showModal"
    header="Hapus data"
    modal
    :closable="false"
  >
    <div class="confirmation-content">
      <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
      <span
        >Anda akan menghapus data pegawai
        <b>{{ props.employee.first_name }} {{ props.employee.last_name }}</b
        >. Anda tidak bisa mengembalikan data setelah anda melanjutkan
        penghapusan!</span
      >
    </div>
    <template #footer>
      <Button label="Batalkan" icon="pi pi-times" text @click="closeModal" />
      <Button
        label="Lanjutkan hapus data"
        icon="pi pi-check"
        text
        @click="deleteEmployee"
      />
    </template>
  </Dialog>

  <Dialog v-model:visible="loading" header="Loading" modal :closable="false">
  </Dialog>
</template>

<script setup>
import axios from "axios";
import { ref, defineProps, defineEmits, watch } from "vue";
import Toast from "primevue/toast";
import { useToast } from "primevue/usetoast";
const toast = useToast();
const props = defineProps({
  showModalProps: {
    type: Boolean,
    default: false,
  },
  employee: Object,
});

watch(
  () => props.showModalProps,
  (newVal) => {
    if (newVal) {
      showModal.value = newVal;
    }
  }
);

const showModal = ref(false);

const emit = defineEmits(["close", "delete"]);
const closeModal = () => {
  showModal.value = false;
  emit("close", false);
};
const loading = ref(false);
const deleteEmployee = async () => {
  loading.value = true;
  await axios.delete(
    import.meta.env.VITE_BASE_URL + props.employee.employee_id
  );
  loading.value = false;
  closeModal();
  emit("delete");

  toast.add({
    severity: "success",
    summary: "Berhasil",
    detail: `Data pegawai telah dihapus`,
    life: 5000,
  });
};
</script>