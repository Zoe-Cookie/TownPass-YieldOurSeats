<script setup lang="ts">
import { computed, ref, reactive } from 'vue';
import { storeToRefs } from 'pinia';
import { useFormStore } from '@/stores/form';
import { useUserStore } from '@/stores/user';
import BaseInput from '@/components/atoms/BaseInput.vue';
import BaseButton from '@/components/atoms/BaseButton.vue';
import BaseDialog from '@/components/atoms/BaseDialog.vue';

export interface BasicForm {
  name: string;
  id: string;
  phone_number: string;
  mail: string;
}

const props = withDefaults(
  defineProps<{
    triggerValidate?: boolean;
    submitForm?: any;
  }>(),
  {
    triggerValidate: false
  }
);

const form = reactive<BasicForm>({
  name: '',
  id: '',
  phone_number: '',
  mail: ''
});

const store = useFormStore();

const { formFormat } = storeToRefs(store);

const userStore = useUserStore();

const { user } = storeToRefs(userStore);

const formFormatMap = computed(
  () => new Map(formFormat.value.data.form_format.map((item: { field: any }) => [item.field, item]))
);

const applyFieldTextHandle = (field: string) => {
  const item: any = formFormatMap.value.get(field);

  if (item.type === 'input' || item.type === 'select' || item.type === 'textarea') {
    return props.submitForm[field];
  } else if (item.type === 'multiple_select' || item.type === 'checkbox_group') {
    const map = new Map(
      item.options.map((el: { value: string | number; label: string }) => [el.value, el.label])
    );

    return props.submitForm[field].map((val: string | number) => map.get(val)).join();
  } else if (item.type === 'radio_group') {
    const map = new Map(
      item.options.map((el: { value: string | number; label: string }) => [el.value, el.label])
    );

    return map.get(props.submitForm[field]);
  } else if (item.type === 'date_picker') {
    return props.submitForm[field]
      .toLocaleDateString('zh-TW', {
        year: 'numeric',
        month: '2-digit',
        day: '2-digit'
      })
      .replace(/\//g, '-');
  } else {
    return '';
  }
};

// const createImgPreviewUrl = (img: File) => {
//   return URL.createObjectURL(img);
// };

const isAgree = ref('Y');

const onSubmitClick = async () => {
  console.log('submitForm:', props.submitForm);

  /**
   * 註解區塊是串接提交表單 API 的範例
   * 透過 JS 原生 fetch 做串接
   * 開發者可以用自己習慣的方式去做 API 串接
   * 例如：axios 等
   */

  // try {
  //   const response = await fetch(formFormat.value.data.submit_target.url, {
  //     method: formFormat.value.data.submit_target.method,
  //     headers: {
  //       'Content-Type': formFormat.value.data.submit_target.content_type
  //     },
  //     body: JSON.stringify(props.submitForm)
  //   });

  //   if (!response.ok) {
  //     throw new Error(`request error: ${response.status}!!`);
  //   }

  //   const responseData = await response.json();
  //   console.log('success upload:', responseData);
  //   isFinishDialogOpen.value = true;
  // } catch (error) {
  //   console.log('error:', error);
  // }

  isFinishDialogOpen.value = true;
};

const isFinishDialogOpen = ref(false);

const onPositiveClick = () => {
  window.location.reload();
};

const onNegativeClick = () => {};
</script>

<template>
  <div class="pb-8">
    <section class="bg-grey-50 px-4 pt-5 pb-4">
      <p class="font-bold mt-4">Hi {{ user?.username }}，請填寫以下基本資料以使用本服務</p>
    </section>
    <form @submit.stop="">
      <div class="p-4 flex">
        <img src="@/assets/images/person-icon.svg" />
        <span class="ml-2 font-bold">基本資料</span>
      </div>
      <div class="p-3">
        <label for="name" class="p-3"> 姓名 </label>
        <BaseInput
          id="name"
          class="w-full"
          placeholder="請輸入姓名"
          :required="true"
          :triggerValidate="props.triggerValidate"
          label="姓名"
          v-model="form.name"
        />
      </div>
      <div class="p-3">
        <label for="id" class="p-3"> 身分證字號 </label>
        <BaseInput
          id="id"
          class="w-full"
          placeholder="請輸入身分證字號"
          :required="true"
          :triggerValidate="props.triggerValidate"
          label="身分證字號"
          v-model="form.id"
        />
      </div>
      <div class="p-3">
        <label for="phone_number" class="p-3"> 手機號碼 </label>
        <BaseInput
          id="phone_number"
          class="w-full"
          placeholder="請輸入手機號碼"
          :required="true"
          :triggerValidate="props.triggerValidate"
          label="手機號碼"
          v-model="form.phone_number"
        />
      </div>
      <div class="p-3">
        <label for="mail" class="p-3"> 電子郵件 </label>
        <BaseInput
          id="mail"
          class="w-full"
          placeholder="請輸入電子郵件"
          :required="true"
          :triggerValidate="props.triggerValidate"
          label="電子郵件"
          v-model="form.mail"
        />
      </div>
    </form>
    <section>
      <ul class="px-4 py-2 flex flex-col gap-y-4">
        <li
          v-for="item in formFormat?.data?.form_format ?? []"
          :key="item.field"
          class="preview-item"
          :class="{ 'preview-item--attachment': item.field === 'attachments' }"
        >
          <!-- 附件先移除 -->
          <!-- <template v-if="item.field === 'attachments'">
            <span class="field-name">附件</span>
            <div class="flex flex-wrap gap-4 mt-2">
              <div v-for="item in fileList" :key="item.name">
                <img
                  v-if="item?.type.includes('image')"
                  :src="createImgPreviewUrl(item)"
                  class="w-24 h-24 object-cover"
                />
                <div v-else class="w-24 h-24 bg-gray-100 flex justify-center items-center px-2">
                  <p class="text-sm">{{ item?.name }}</p>
                </div>
              </div>
            </div>
          </template> -->
          <span class="field-name">{{ item.label }}</span>
          <span>{{ applyFieldTextHandle(item.field) }}</span>
        </li>
      </ul>
    </section>
    <div class="grid gap-x-2 px-4 mt-4">
      <BaseButton :disabled="isAgree === 'N'" @click="onSubmitClick">修改 / 確認</BaseButton>
    </div>
    <BaseDialog
      v-model="isFinishDialogOpen"
      title="資料已登錄"
      content="現在可以開始使用本服務"
      positiveText="前往查看"
      negativeText="確認"
      @onPositiveClick="onPositiveClick"
      @onNegativeClick="onNegativeClick"
    />
  </div>
</template>

<style lang="postcss">
.field-name {
  @apply text-gray-500;
}

.preview-item {
  @apply flex justify-between;

  &--attachment {
    @apply flex-col justify-start;
  }
}
</style>
