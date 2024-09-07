<script setup lang="ts">
import { storeToRefs } from 'pinia';
import { useUserStore } from '@/stores/user';

export interface BasicForm {
  name: string;
  id: string;
  phone_number: string;
  mail: string;
}

const userStore = useUserStore();

const { user } = storeToRefs(userStore);
</script>

<template>
  <div class="pb-8">
    <section class="bg-grey-50 px-4 pt-5 pb-4">
      <p class="font-bold mt-4">Hi {{ user?.username }}，歡迎使用本服務！服務之詳細說明如下：</p>
    </section>
    <form>
      <div class="p-3">
        <label for="name" class="p-3">
          1. 服務適用對象：年長者、孕婦、孩童、行動不便者及其他特殊情況需要座位的乘客。
        </label>
      </div>
      <div class="p-3">
        <label for="id" class="p-3">
          2.
          本服務旨在服務需要座位的乘客，並解決讓座鈴被誤按、需要座位者不易辨識等問題，按下畫面中的按鈕即可播放讓座鈴，歡迎多加使用。
        </label>
      </div>
      <div class="p-3">
        <label for="phone_number" class="p-3">
          3. 因本服務的使用並無強制性，若遇人潮眾多、無人讓位之情形，請嘗試理性與他人溝通。
        </label>
      </div>
      <div class="p-3">
        <label for="mail" class="p-3"> 4. 若使用上遇到任何問題，歡迎回報與我們聯繫。 </label>
      </div>
    </form>
  </div>
</template>
