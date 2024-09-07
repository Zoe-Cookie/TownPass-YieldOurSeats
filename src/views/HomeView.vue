<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { useFormStore } from '@/stores/form';
import { useUserStore } from '@/stores/user';
import { storeToRefs } from 'pinia';
import { useConnectionMessage } from '@/composables/useConnectionMessage';
import { useHandleConnectionData } from '@/composables/useHandleConnectionData';
import ServiceTabsView from '@/components/organisms/ServiceTabsView.vue';
import type { User } from '@/stores/user';
import FormView from './FormView.vue';
import BaseButton from '@/components/atoms/BaseButton.vue';

const audio = ref<HTMLAudioElement | null>(null);

onMounted(() => {
  audio.value = new Audio('/737894__jadis0x__simple-piano-melody-loop.wav');
});

const store = useFormStore();

store.reset();

const userStore = useUserStore();

const { user } = storeToRefs(userStore);

const handleUserInfo = (event: { data: string }) => {
  const result: { name: string; data: User } = JSON.parse(event.data);
  user.value = result.data;
};

useConnectionMessage('userinfo', null);

useHandleConnectionData(handleUserInfo);

const route = useRoute();

const activeTab = ref(0);

if (route.query.isSearch) {
  activeTab.value = 1;
}

const notify = ref(false);

const playSound = () => {
  notify.value = !notify.value;
  if (notify.value && audio.value) {
    audio.value.play();
  }
  else if(audio.value){
    audio.value.pause();
  }
};
</script>

<template>
  <main>
    <ServiceTabsView v-model="activeTab">
      <template #tab0>
        <div class="p-4 flex items-center">
          <img src="/images/pregnancy.png" alt="pregnancy" class="h-24" />
          <div>
            <h1 class="text-xl font-bold">傳遞馨聲 - 讓座鈴微服務</h1>
            <h1 class="text-lg">有座位需求勇敢說，讓座愛心不分你我</h1>
          </div>
          <img src="/images/old-man.png" alt="old-man" class="h-20" />
        </div>
        <div class="grid grid-cols-1 flex justify-center items-center">
          <BaseButton @click="playSound" class="m-2">開啟/關閉鈴聲</BaseButton>
          <h1 v-if="notify" class="m-2">鈴聲已開啟✔</h1>
          <h1 v-else class="m-2">鈴聲已關閉✘</h1>
        </div>
      </template>
      <template #tab1>
        <FormView></FormView>
      </template>
    </ServiceTabsView>
  </main>
</template>
