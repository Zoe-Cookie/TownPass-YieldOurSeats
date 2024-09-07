<script setup lang="ts">
import { ref } from 'vue';
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

const toggle = () => {
  notify.value = !notify.value;
};
</script>

<template>
  <main>
    <ServiceTabsView v-model="activeTab">
      <template #tab0>
        <div class="p-4 flex items-center">
          <img src="../../public/images/pregnancy.png" alt="pregnancy" class="h-24 italic" />
          <div>
            <h1 class="text-xl font-bold">祝你好孕-讓座微服務</h1>
            <h1 class="text-lg">每個人都曾是媽媽肚子理的孩子，體貼孕婦人人有責</h1>
          </div>
        </div>
        <div class="grid grid-cols-1 flex justify-center items-center">
          <BaseButton @click="toggle" class="m-2">開啟/關閉通知</BaseButton>
          <h1 v-if="notify" class="m-2">提醒功能：已開啟✔</h1>
          <h1 v-else class="m-2">提醒功能：已關閉✘</h1>
        </div>
      </template>
      <template #tab1>
        <FormView></FormView>
      </template>
    </ServiceTabsView>
  </main>
</template>
