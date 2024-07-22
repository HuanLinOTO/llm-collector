<template>
    <div class="container">
      <form @submit.prevent="handleSubmit">
        <div>
          <label for="outputCount">字幕数量:</label>
          <n-input-number v-model:value="outputCount" clearable />
        </div>
        <div>
          <label for="notesCount">侧重点/其他 数量:</label>
          <n-input-number v-model:value="notesCount" clearable />
        </div>
  
        <n-card class="cards" v-for="(output, index) in form.output" :key="index">
          英文字幕 {{ index + 1 }}:
          <n-input v-model:value="form.output[index].script" />
  
          中文字幕 {{ index + 1 }}:
          <n-input v-model:value="form.output[index].script_cn" />
        </n-card>
  
        <div v-for="(note, index) in form.notes" :key="index">
          侧重点/其他 {{ index + 1 }}:
          <n-input v-model:value="form.notes[index].note" />
        </div>
  
        <n-button class="submit-btn" type="success" @click="handleSubmit">下载文件</n-button>
      </form>
    </div>
</template>

<script setup>
import { reactive, ref, watch } from 'vue';
import { useMessage } from "naive-ui"

const message = useMessage();

const form = reactive({
  output: [
    {
      script: '',
      script_cn: ''
    }
  ],
  notes: [
    {
      note: ''
    }
  ]
});

const outputCount = ref(1);
const notesCount = ref(1);

const handleSubmit = () => {
  // console.log(form);
  
  // 检查 form 中是否有空项
  for (const key in form) {
    if (Array.isArray(form[key])) {
      for (const item of form[key]) {
        for (const k in item) {
          if (!item[k]) {
            message.info('请填写完整');
            return;
          }
        }
      }
    }
  } 
  
  // 把 form 变成 .json 文件下载下来，取名为时间戳
  const data = JSON.stringify(form);
  const blob = new Blob([data], { type: 'application/json' });

  const url = URL.createObjectURL(blob);

  const a = document.createElement('a');
  a.href = url;
  a.download = `${Date.now()}.json`;
  a.click();

};

watch(outputCount, (val) => {
  form.output = Array.from({ length: val }, () => ({
    script: '',
    script_cn: ''
  }));
});

watch(notesCount, (val) => {
  form.notes = Array.from({ length: val }, () => ({
    note: ''
  }));
});

</script>

<style scoped>
.container {
  margin: 0 auto;
  max-width: 600px;
  padding: 20px;
}
.submit-btn {
  margin-top: 20px;
  width: 100%;
}
.cards {
  margin-top: 20px;
  shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>
