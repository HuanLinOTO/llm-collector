<template>
    <div class="container">
      原视频字幕

      <n-input
        v-model:value="raw_script"
        type="textarea"
      />

      <!-- <div>
        字幕数量:
        <n-input-number v-model:value="outputCount" clearable />
      </div> -->
      
      <n-h1>以下均为 AI 输出结果</n-h1>

      <n-card class="cards" v-for="(item, key) in form.output" :key="key">
        英文字幕 {{ key2desc[key] }}:
        <n-input v-model:value="form.output[key].script" type="textarea"/>
        
        中文字幕 {{ key2desc[key] }}:
        <n-input v-model:value="form.output[key].script_cn" type="textarea"/>
      </n-card>
      
      <div>
        侧重点/其他 数量:
        <n-input-number v-model:value="notesCount" clearable />
      </div>
      <div v-for="(note, index) in form.notes" :key="index">
        侧重点/其他 {{ index + 1 }}:
        <n-input v-model:value="form.notes[index].note" type="textarea"/>
      </div>

      <n-button class="submit-btn" type="success" @click="handleSubmit">下载文件</n-button>
    </div>
</template>

<script setup>
import { reactive, ref, watch } from 'vue';
import { useMessage } from "naive-ui"

const message = useMessage();

const raw_script = ref('');

const key2desc = {
  hook: '钩子',
  desc: '产品介绍',
  share: '产品背书(安利)',
  up: '提出问题/制造焦虑',
  solution: '解决焦虑/产品使用',
  cta: 'CTA'
};

const form = reactive({
  output: {
    hook: {
      script: '',
      script_cn: ''
    },
    desc: {
      script: '',
      script_cn: ''
    },
    share: {
      script: '',
      script_cn: ''
    },
    up: {
      script: '',
      script_cn: ''
    },
    solution: {
      script: '',
      script_cn: ''
    },
    cta: {
      script: '',
      script_cn: ''
    },
  },
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

  if (!raw_script.value) {
    message.info('请填写原视频字幕');
    return;
  }
  
  // 把 form 变成 .json 文件下载下来，取名为时间戳

  const data = JSON.stringify({...form, raw_script: raw_script.value}); 
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
