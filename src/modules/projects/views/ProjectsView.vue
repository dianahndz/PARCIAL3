<template>
  <div class="overflow-x-auto w-full p-8 bg-white shadow-md rounded-lg">
    <h2 class="text-2xl font-bold mb-4 text-center">Lista de Proyectos</h2>
    <table class="table-auto w-full bg-gray-100 rounded-lg">
      <!-- head -->
      <thead>
        <tr class="bg-blue-500 text-white">
          <th class="py-2 px-4">#</th>
          <th class="py-2 px-4">Proyecto</th>
          <th class="py-2 px-4">Tareas</th>
          <th class="py-2 px-4">Avance</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(proyecto, i) in proyectosStore.proyectos"
          :key="i"
          class="hover:bg-gray-200 transition duration-300"
        >
          <th class="py-2 px-4">{{ i + 1 }}</th>
          <td class="py-2 px-4">{{ proyecto.nombre }}</td>
          <td class="py-2 px-4">{{ proyecto.tareas.length }}</td>
          <td class="py-2 px-4">
            <progress
              :value="proyecto.progreso"
              class="progress progress-primary w-56"
              max="100"
            ></progress>
          </td>
        </tr>
      </tbody>
    </table>
    
    <input-modal
      :open="modalOpen"
      @close="modalOpen = false"
      @value="onNewValue"
      placeholder="Ingrese el nombre del proyecto"
      title="Nuevo proyecto"
      sub-title="Dale un nombre único a tu proyecto"
    />

    <custom-modal :open="customModalOpen">
      <template #header>
        <h1 class="text-3xl">Titulo del modal</h1>
      </template>

      <template #body>
        <p>
          Nulla consequat non ullamco mollit est quis duis pariatur cupidatat consequat Lorem cillum.
        </p>
      </template>

      <template #footer>
        <div class="flex justify-end">
          <button @click="customModalOpen = false" class="btn mr-4">Close</button>
          <button @click="customModalOpen = false" class="btn btn-primary">Aceptar</button>
        </div>
      </template>
    </custom-modal>

    <fab-button @click="modalOpen = true" position="bottom-right">
      <AddCircle />
    </fab-button>

    <fab-button @click="customModalOpen = true" position="bottom-left">
      <ModalIcon />
    </fab-button>
  </div>
</template>

<script lang="ts" setup>
import CustomModal from '@/modules/common/components/CustomModal.vue';
import FabButton from '@/modules/common/components/FabButton.vue';
import InputModal from '@/modules/common/components/InputModal.vue';
import AddCircle from '@/modules/common/icons/AddCircle.vue';
import ModalIcon from '@/modules/common/icons/ModalIcon.vue';
import { ref } from 'vue';
import { useProyectosStore } from '../store/projects.store';

const proyectosStore = useProyectosStore();
const modalOpen = ref(false);
const customModalOpen = ref(false);

const onNewValue = (nombreProyecto: string) => {
  proyectosStore.agregarProyecto(nombreProyecto);
  modalOpen.value = false; // Cierra el modal después de agregar un nuevo proyecto
};
</script>

<style scoped>
.table {
  border-collapse: collapse;
  width: 100%;
}

th,
td {
  border: 1px solid #e5e7eb; /* gray-300 */
  text-align: left;
  padding: 0.5rem;
}

th {
  background-color: #3b82f6; /* blue-500 */
  color: white;
}

.progress {
  height: 1rem;
}
</style>
