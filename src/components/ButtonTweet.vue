<script setup lang="ts">
import { postTweet } from '@/services/api';
import { emit } from 'process';
import { ref } from 'vue';

const emit = defineEmits(['addTweet']);
const content = ref<string>('');
const isTweeting = ref<boolean>(false);
const hasMessage = ref<boolean>(false);
const message = ref<string>('');
const messageTimeout = ref<number>(-1);
const alertType = ref<string>('');
const closeModal = ref<boolean>(false);
const spinnerLoading = ref<boolean>(false);
const maxContentLength = 280;

async function handlePostTweet() {
  spinnerLoading.value = true;

  emit('updateKey', new Date().getTime());

  if (content.value == '') {
    spinnerLoading.value = false;
    showMessage('Você não pode publicar um tweet vazio.', 'error');
    closeModal.value = false;
    return;
  }

  isTweeting.value = true;
  const res = await postTweet(content.value);

  if (!res?.data.success) {
    spinnerLoading.value = false;
    showMessage('Erro ao publicar tweet', 'error');
    return;
  }

  emit('addTweet');

  closeModal.value = false; // Fechar modal após mostrar a mensagem

  showMessage('Tweet publicado com sucesso!', 'success');
  content.value = '';
  isTweeting.value = false;
}

function showMessage(messageText: string, type: string) {
  hasMessage.value = true;
  message.value = messageText;
  alertType.value = type;

  if (messageTimeout.value) clearTimeout(messageTimeout.value);
  messageTimeout.value = setTimeout(() => {
    hasMessage.value = false;
  }, 3000);
}

function clearMessage() {
  clearTimeout(messageTimeout.value);
  hasMessage.value = false;
}
</script>

<template>
  <v-btn
    @click="closeModal.value = true"
    class="pe-2 tweet-btn"
    prepend-icon="mdi-feather"
    variant="flat"
  >
    <div class="text-none mobile-text-none font-weight-regular">Tweetar</div>
    <v-model>
      <v-alert
        v-if="hasMessage"
        closable
        class="alert fixed-alert"
        :text="message"
        :color="alertType"
        @click:close="clearMessage()"
      ></v-alert>
    </v-model>

    <v-dialog v-model="closeModal" activator="parent" max-width="500" class="full-screen-dialog">
      <template v-slot:default="{ isActive }">
        <v-card>
          <v-card-title class="d-flex justify-space-between align-center">
            <div class="text-h5 text-medium-emphasis ps-2">Tweetar</div>

            <v-btn icon="mdi-close" variant="text" @click="isActive.value = false"></v-btn>
          </v-card-title>

          <v-divider class="mb-4"></v-divider>

          <!-- Modal Body -->
          <v-card-text>
            <v-textarea
              label="O que está acontecendo?"
              v-model="content"
              :counter="maxContentLength"
              :maxlength="maxContentLength"
              class="mb-2"
              rows="5"
              variant="outlined"
              persistent-counter
            ></v-textarea>
          </v-card-text>

          <v-divider class="mt-2"></v-divider>

          <!-- Modal Footer -->
          <v-card-actions class="my-2 d-flex justify-end">
            <v-btn
              class="text-none"
              rounded="xl"
              text="Cancel"
              @click="isActive.value = false"
            ></v-btn>

            <v-btn
              class="text-none"
              color="primary"
              rounded="xl"
              text="Tweetar"
              variant="flat"
              @click="handlePostTweet"
              :disabled="isTweeting"
            >
              <p v-if="!spinnerLoading">Tweetar</p>
              <v-progress-circular
                class="spinner"
                v-if="spinnerLoading"
                indeterminate
              ></v-progress-circular>
            </v-btn>
          </v-card-actions>
        </v-card>
      </template>
    </v-dialog>
  </v-btn>
</template>

<style scoped>
@media (max-width: 500px) {
  #tweet-card {
    margin-top: -28px;
    margin-left: -25px;
    margin-bottom: -25px;
    height: 100vh !important;
    width: 100vw !important;
    border-radius: 0 !important;
  }
  .modal {
    max-height: 100% !important;
  }
}
@media (max-width: 1200px) {
  .mobile-text-none {
    display: none;
  }
}
@media (max-width: 600px) {
  .full-screen-dialog .v-card {
    width: 100% !important;
    height: 100vh !important;
    max-width: 100% !important;
    max-height: 100vh !important;
    margin: 0 !important;
    border-radius: 0 !important;
    padding: 0 !important;
  }
  .mobile-text-none {
    display: none;
  }
}

.v-btn.tweet-btn {
  padding: 1em 1.5em 1em;
  background-color: #4285f4;
  border: none;
  color: white;
  border-radius: 21px;
  cursor: pointer;
  font-size: 1em;
  display: flex;
  align-items: center;
}
.v-btn.tweet-btn:hover {
  background-color: #357ae8;
}

.v-btn.tweet-btn {
  padding: 1em 1.5em 1em;
  background-color: #4285f4;
  border: none;
  color: white;
  border-radius: 21px;
  cursor: pointer;
  font-size: 1em;
  display: flex;
  align-items: center;
}
.v-btn.tweet-btn:hover {
  background-color: #357ae8;
}

.alert {
  margin: 0 24px 24px !important;
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 9999;
  width: auto;
  max-width: 90%;
}

.modal {
  position: fixed;
  z-index: 9998;
}

.spinner {
  width: 1.5rem;
}
</style>
