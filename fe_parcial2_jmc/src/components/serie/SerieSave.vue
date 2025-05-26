<script setup lang="ts">
import type { Serie } from '@/models/serie'
import http from '@/plugins/axios'
import { Calendar, DatePicker, InputNumber, Select } from 'primevue'
import Button from 'primevue/button'
import Dialog from 'primevue/dialog'
import InputText from 'primevue/inputtext'
import { computed, ref, watch } from 'vue'

const ENDPOINT = 'series'
const props = defineProps({
  mostrar: Boolean,
  serie: {
    type: Object as () => Serie,
    default: () => ({}) as Serie,
  },
  modoEdicion: Boolean,
})
const emit = defineEmits(['guardar', 'close'])

const dialogVisible = computed({
  get: () => props.mostrar,
  set: (value) => {
    if (!value) emit('close')
  },
})

const serie = ref<Serie>({ ...props.serie })
watch(
  () => props.serie,
  (newVal) => {
    serie.value = { ...newVal }
  },
)

const geneross = [
  { name: 'Drama', value: 'Drama' },
  { name: 'Comedia', value: 'Comedia' },
  { name: 'Acción', value: 'Acción' },
  { name: 'Terror', value: 'Terror' },
  { name: 'Ciencia Ficción', value: 'Ciencia Ficción' },
]

async function handleSave() {
  try {
    const body = {
      titulo: serie.value.titulo,
      genero: serie.value.genero,
      sinopsis: serie.value.sinopsis,
      director: serie.value.director,
      temporadas: serie.value.temporadas,
      fechaEstreno: serie.value.fechaEstreno,
    }
    if (props.modoEdicion) {
      await http.patch(`${ENDPOINT}/${serie.value.id}`, body)
    } else {
      await http.post(ENDPOINT, body)
    }
    emit('guardar')
    serie.value = {} as Serie
    dialogVisible.value = false
  } catch (error: any) {
    alert(error?.response?.data?.message)
  }
}
</script>

<template>
  <div class="card flex justify-center">
    <Dialog
      v-model:visible="dialogVisible"
      :header="props.modoEdicion ? 'Editar' : 'Crear'"
      style="width: 25rem"
    >
      <div class="flex items-center gap-4 mb-4">
        <label for="titulo" class="font-semibold w-6">Titulo:</label>
        <InputText
          id="titulo"
          v-model="serie.titulo"
          class="flex-auto"
          autocomplete="off"
          autofocus
        />
      </div>
      <div class="flex items-center gap-4 mb-4">
        <label for="genero" class="font-semibold w-6">Género:</label>
        <Select
          id="genero"
          v-model="serie.genero"
          :options="geneross"
          optionLabel="name"
          optionValue="value"
          placeholder="Seleccionar Genero"
          class="w-full md:w-56"
        />
      </div>
      <div class="flex items-center gap-4 mb-4">
        <label for="sinopsis" class="font-semibold w-6">Sinopsis:</label>
        <InputText
          id="sinopsis"
          v-model="serie.sinopsis"
          class="flex-auto"
          autocomplete="off"
          autofocus
        />
      </div>
      <div class="flex items-center gap-4 mb-4">
        <label for="director" class="font-semibold w-6">Director:</label>
        <InputText
          id="director"
          v-model="serie.director"
          class="flex-auto"
          autocomplete="off"
          autofocus
        />
      </div>
      <div class="flex items-center gap-4 mb-4">
        <label for="temporadas" class="font-semibold w-6">Temporadas:</label>
        <InputNumber
          id="temporadas"
          v-model="serie.temporadas"
          class="flex-auto"
          autocomplete="off"
          autofocus
        />
      </div>
      <div class="flex items-center gap-4 mb-4">
        <label for="fechaestreno" class="font-semibold w-6">Fecha de Estreno:</label>
        <DatePicker
          id="fechaEstreno"
          v-model="serie.fechaEstreno"
          showIcon
          fluid
          iconDisplay="input"
        />
      </div>
      <div class="flex justify-end gap-2">
        <Button
          type="button"
          label="Cancelar"
          icon="pi pi-times"
          severity="secondary"
          @click="dialogVisible = false"
        ></Button>
        <Button type="button" label="Guardar" icon="pi pi-save" @click="handleSave"></Button>
      </div>
    </Dialog>
  </div>
</template>

<style scoped></style>
