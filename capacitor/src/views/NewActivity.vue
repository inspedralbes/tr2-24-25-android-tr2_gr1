<template>
  <div class="screen-container">
    <Card>
      <template #title>Nova Proposta</template>
      <template #content>
        <div class="card flex flex-wrap justify-center items-end gap-4">
          <FloatLabel variant="in">
            <label for="titol_label">Títol</label>
            <InputText id="titol_label" v-model="titol" autocomplete="off" />
          </FloatLabel>
        </div>

        <div class="card flex flex-wrap justify-center items-end gap-4">
          <FloatLabel variant="in">
            <label for="subtitol_label">Subtítol</label>
            <InputText id="subtitol_label" v-model="subtitol" autocomplete="off" />
          </FloatLabel>
        </div>

        <div class="card flex flex-wrap justify-center items-end gap-4">
          <FloatLabel variant="in">
            <label for="contingut_label">Contingut</label>
            <InputText id="contingut_label" v-model="contingut" autocomplete="off" />
          </FloatLabel>
        </div>

        <div class="card flex flex-wrap justify-center items-end gap-4">
          <FloatLabel variant="in">
            <label for="data_label">Data</label>
            <InputText id="data_label" v-model="data" type="date" />
          </FloatLabel>
        </div>

        <div class="card flex flex-wrap justify-center items-end gap-4">
          <label for="color_picker">Color de la Proposta</label>
          <ColorPicker id="color_picker" v-model="color" format="hex" />
        </div>

        <div class="card flex flex-wrap justify-center items-end gap-4">
          <Button
            class="button secondary-button"
            label="Crear"
            icon="pi pi-plus"
            iconPos="left"
            :loading="loading"
            @click="newProposta"
          />
        </div>

        <Dialog :visible="visible" modal header="Atenció" :style="{ width: '25rem' }" class="bg-white">
          <span class="text-surface-500 dark:text-surface-400 block mb-8">Es requereixen tots els camps obligatoris</span>
          <div class="flex justify-end gap-2">
            <Button class="button secondary-button" type="button" label="Entesos" @click="visible = false" />
          </div>
        </Dialog>

        <Dialog :visible="visible2" modal header="Perfecte!" :style="{ width: '25rem' }" class="bg-white">
          <span class="text-surface-500 dark:text-surface-400 block mb-8">La proposta s'ha creat correctament</span>
          <div class="flex justify-end gap-2">
            <Button class="button secondary-button" type="button" label="Entesos" @click="visible2 = false" />
          </div>
        </Dialog>

        <Dialog :visible="visible3" modal header="Error" :style="{ width: '25rem' }" class="bg-white">
          <span class="text-surface-500 dark:text-surface-400 block mb-8">Hi ha hagut un error en crear la proposta</span>
          <div class="flex justify-end gap-2">
            <Button class="button secondary-button" type="button" label="Entesos" @click="visible3 = false" />
          </div>
        </Dialog>
      </template>
    </Card>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import InputText from 'primevue/inputtext';
import FloatLabel from 'primevue/floatlabel';
import Button from 'primevue/button';
import Dialog from 'primevue/dialog';
import Card from 'primevue/card';
import { crearProposta } from '@/services/comunicationManager';
import ColorPicker from 'primevue/colorpicker';
import { useRouter } from 'vue-router';
import { useLoggedUsers } from '@/stores/users';

const router = useRouter();

const titol = ref(null);
const subtitol = ref(null);
const contingut = ref(null);
const data = ref(null);
const color = ref('#FFFFFF');
const loading = ref(false);
let visible = ref(false);
let visible2 = ref(false);
let visible3 = ref(false);

const loggedUsersStore = useLoggedUsers();

const userId = loggedUsersStore.currentUser?.id;

async function newProposta() {
    if (!titol.value || !subtitol.value || !contingut.value || !data.value) {
        console.error('Campos obligatorios faltantes:', {
            titol: titol.value,
            subtitol: subtitol.value,
            contingut: contingut.value,
            data: data.value,
        });
        visible.value = true;
        return;
    }

    if (!userId) {
        console.error('Debes estar logueado para crear una propuesta.');
        visible3.value = true;
        return;
    }

    loading.value = true;
    console.log('Datos enviados para crear la propuesta:', {
        titol: titol.value,
        subtitol: subtitol.value,
        contingut: contingut.value,
        data: data.value,
        color: color.value,
        userId: userId,
    });

    try {
        await crearProposta(titol.value, subtitol.value, contingut.value, userId, data.value, color.value);
        console.log('Propuesta creada exitosamente.');
        visible2.value = true;

        router.push({ path: '/propostes' });
    } catch (error) {
        console.error('Error al crear propuesta:', error);
        visible3.value = true;
    } finally {
        loading.value = false;
    }
}
</script>

<style scoped>
.screen-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 5px;
  height: 100vh;
  padding: 10px;
  background-color: var(--main-color);
}

.card {
  padding: 10px;
  max-width: 400px;
  width: 100%;
  color: var(--accent-light-color);
}

.secondary-button {
  background-color: var(--secondary-light-color) !important;
  border-color: var(--secondary-light-color) !important;
  color: white !important;
}

.secondary-button:hover {
  filter: brightness(90%);
}

.create-btn {
  background-color: var(--secondary-light-color);
  color: white !important;
}

.create-btn:hover {
  filter: brightness(90%);
}
</style>