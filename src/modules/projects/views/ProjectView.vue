<template>
  <div class="p-8 max-w-4xl mx-auto bg-white shadow-md rounded-lg">
    <h2 class="text-2xl font-bold mb-4 text-center">Nombre Proyecto: {{ proyectoActual?.nombre }}</h2>

    <div class="mt-4 flex items-center mb-6">
      <input
        v-model="nuevaTarea"
        type="text"
        placeholder="Agregar nueva tarea"
        class="input input-bordered mr-2 p-2 border rounded-lg flex-1 focus:outline-none focus:ring focus:ring-blue-300"
      />
      <button @click="agregarTarea" class="btn btn-primary p-2 rounded-lg hover:bg-blue-600 transition duration-300">
        Agregar
      </button>
    </div>

    <table class="table-auto w-full bg-gray-100 rounded-lg shadow-lg">
      <thead>
        <tr class="bg-blue-500 text-white">
          <th class="py-2 px-4">#</th>
          <th class="py-2 px-4">Completada</th>
          <th class="py-2 px-4">Tarea</th>
          <th class="py-2 px-4">Fecha y hora</th>
          <th class="py-2 px-4">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(tarea, index) in proyectoActual?.tareas" :key="tarea.id" class="hover:bg-gray-200 transition duration-300">
          <td class="py-2 px-4">{{ index + 1 }}</td>
          <td class="py-2 px-4">
            <input type="checkbox" v-model="tarea.completada" @change="actualizarProgreso(proyectoActual.id)" />
          </td>
          <td class="py-2 px-4">
            <input v-model="tarea.nombre" type="text" class="input input-sm border rounded-lg p-1 w-full" />
          </td>
          <td class="py-2 px-4">{{ tarea.fechaHora }}</td>
          <td class="py-2 px-4">
            <button @click="actualizarTarea(tarea.id)" class="btn btn-sm btn-secondary mr-2 rounded-lg hover:bg-gray-300">Actualizar</button>
            <button @click="eliminarTarea(tarea.id)" class="btn btn-sm btn-error rounded-lg hover:bg-red-600 text-white">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';
import { useProyectosStore } from '../store/projects.store';

const proyectosStore = useProyectosStore();
const route = useRoute();
const nuevaTarea = ref('');

const proyectoActual = computed(() =>
  proyectosStore.proyectos.find((proyecto) => proyecto.id === route.params.id)
);

const agregarTarea = () => {
  if (nuevaTarea.value.trim() !== '' && proyectoActual.value.tareas.length < 10) {
    proyectosStore.agregarTarea(route.params.id as string, nuevaTarea.value.trim());
    nuevaTarea.value = '';
  } else {
    alert('No puedes agregar más de 10 tareas.');
  }
};

const actualizarTarea = (idTarea: string) => {
  const nuevoNombre = prompt('Ingrese el nuevo nombre de la tarea');
  if (nuevoNombre && proyectoActual.value) {
    proyectosStore.actualizarTarea(route.params.id as string, idTarea, nuevoNombre.trim());
  }
};

const eliminarTarea = (idTarea: string) => {
  if (confirm('¿Estás seguro de que quieres eliminar esta tarea?')) {
    proyectosStore.eliminarTarea(route.params.id as string, idTarea);
  }
};

const actualizarProgreso = (idProyecto: string) => {
  const proyecto = proyectosStore.proyectos.find((p) => p.id === idProyecto);
  if (proyecto) {
    const totalTareas = proyecto.tareas.length;
    const tareasCompletadas = proyecto.tareas.filter(t => t.completada).length;
    proyecto.progreso = (tareasCompletadas / totalTareas) * 100;
  }
};
</script>

<style scoped>
.input {
  border: 1px solid #ccc;
  border-radius: 0.375rem;
  padding: 0.5rem;
  transition: border-color 0.2s;
}

.input:focus {
  border-color: #60a5fa;
  outline: none;
}

.btn {
  background-color: #3b82f6;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  border: none;
  cursor: pointer;
}

.btn-primary:hover {
  background-color: #2563eb;
}

.btn-secondary {
  background-color: #d1fae5;
  color: #065f46;
}

.btn-error {
  background-color: #f87171;
  color: white;
}

.btn-error:hover {
  background-color: #ef4444;
}
</style>
