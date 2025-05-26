<script setup lang="ts">
import type { Serie } from '@/models/serie'
import { onMounted, ref } from 'vue'
import http from '@/plugins/axios'
import { Button, Dialog } from 'primevue'

const ENDPOINT = 'series'
const series = ref<Serie[]>([])
const emit = defineEmits(['edit'])
const serieDelete = ref<Serie | null>(null)
const mostrarConfirmDialog = ref<boolean>(false)

async function obtenerLista() {
  series.value = await http.get(ENDPOINT).then((response) => response.data)
}

function emitirEdicion(serie: Serie) {
  emit('edit', serie)
}

function mostrarEliminarConfirm(serie: Serie) {
  serieDelete.value = serie
  mostrarConfirmDialog.value = true
}

async function eliminar() {
  await http.delete(`${ENDPOINT}/${serieDelete.value?.id}`)
  obtenerLista()
  mostrarConfirmDialog.value = false
}
onMounted(() => {
  obtenerLista()
})
defineExpose({ obtenerLista })
</script>

<template>
  <div>
    <table style="border-collapse: collapse" border="1">
      <thead>
        <tr>
          <th>Nro.</th>
          <th>Titulo</th>
          <th>Genero</th>
          <th>Sinopsis</th>
          <th>Director</th>
          <th>Temporadas</th>
          <th>Fecha de Estreno</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(serie, index) in series" :key="serie.id">
          <td>{{ index + 1 }}</td>
          <td>{{ serie.titulo }}</td>
          <td>{{ serie.genero }}</td>
          <td>{{ serie.sinopsis }}</td>
          <td>{{ serie.director }}</td>
          <td>{{ serie.temporadas }}</td>
          <td>{{ serie.fechaEstreno }}</td>
          <td>
            <Button icon="pi pi-pencil" aria-label="Editar" text @click="emitirEdicion(serie)" />
            <Button
              icon="pi pi-trash"
              aria-label="Eliminar"
              severity="danger"
              @click="mostrarEliminarConfirm(serie)"
            />
          </td>
        </tr>
      </tbody>
    </table>

    <Dialog
      v-model:visible="mostrarConfirmDialog"
      header="Confirmar Eliminación"
      :style="{ width: '25rem' }"
    >
      <p>¿Estás seguro de que deseas eliminar este registro?</p>
      <br />
      <div class="flex justify-end gap-2">
        <Button
          type="button"
          label="Cancelar"
          severity="secondary"
          @click="mostrarConfirmDialog = false"
        />
        <Button type="button" label="Eliminar" @click="eliminar" />
      </div>
    </Dialog>
  </div>
</template>

<style scoped></style>
