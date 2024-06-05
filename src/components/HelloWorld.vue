<template>
  <a-typography-title style="text-align: center">
    抖音主播信息查询
  </a-typography-title>
  <a-typography-title :heading="6" style="text-align: center" type="secondary">
    支持10w+直播间具体人数查询
  </a-typography-title>
  <div class="main">
    <a-form :model="form" layout="vertical" @submit="handleSubmit">
      <a-form-item field="name" label="主播昵称" tooltip="要保证第一个搜到的是他">
        <a-input v-model="form.keyword" placeholder="" />
      </a-form-item>
      <a-form-item disabled field="post" label="主播id">
        <a-input v-model="form.web_rid" placeholder="暂不支持" />
      </a-form-item>
      <a-form-item>
        <a-button html-type="submit">查询</a-button>
      </a-form-item>
    </a-form>
    <div v-if="card_data.is_load" class="loading-spinner">加载中...</div>
    <div v-else>
      <a-card v-for="(data, index) in card_data.list" :key="index" :loading="card_data.is_load">
        <template #actions>
          <span class="icon-hover" @click="gotoDouyin(data.web_rid)"> <IconTiktokColor/> </span>
          <span class="icon-hover" @click="share(data)"> <IconShareInternal/> </span>
        </template>
        <a-card-meta :description="'在线观众 : '+data.display_value" :title="data.title">
          <template #avatar>
            <div :style="{ display: 'flex', alignItems: 'center', color: '#1D2129' }">
              <a-avatar v-show="data.display_value!==0" :size="60" :style="{ marginRight: '8px' }">
                <img :src="data.avatar" :style="{ width: '100%' }" alt="avatar" />
              </a-avatar>
              <a-typography-text>{{ data.nickname }}</a-typography-text>
            </div>
          </template>
        </a-card-meta>
      </a-card>
    </div>
  </div>
</template>

<script setup>
import { IconShareInternal, IconTiktokColor } from '@arco-design/web-vue/es/icon';
import { getCurrentInstance, reactive } from 'vue';
import axios from 'axios';

let $message = getCurrentInstance().appContext.config.globalProperties.$message;

const form = reactive({
  keyword: '',
  web_rid: '',
  isRead: false,
});

const card_data = reactive({
  list: [],
  is_load: false
});

const handleSubmit = async () => {
  try {
    card_data.is_load = true;
    const response = await axios.get(import.meta.env.VITE_BASE_URL +"?keyword="+ form.keyword);
    card_data.list = response.data.map(item => ({
      avatar: item.avatar,
      display_value: item.display_value,
      nickname: item.nickname,
      title: item.title,
      web_rid: item.web_rid,
    }));
  } catch (error) {
    console.error('Error:', error);
  } finally {
    card_data.is_load = false;
  }
};

const gotoDouyin = (web_rid) => {
  window.open(`https://live.douyin.com/${web_rid}`);
};

const share = (data) => {
  const text = `我正在看: ${data.nickname} \n在线观众：${data.display_value}\n 直播间网址：https://live.douyin.com/${data.web_rid}`;
  const input = document.createElement('input');
  input.setAttribute('readonly', 'readonly');
  input.value = text;
  document.body.appendChild(input);
  input.select();
  if (document.execCommand('copy')) {
    document.execCommand('copy');
    $message.success('分享信息已复制到剪贴板');
  } else {
    $message.error('分享信息复制失败');
  }
  document.body.removeChild(input);
};
</script>

<style>
.arco-form-item-content-flex {
  justify-content: flex-end;
}
.loading-spinner {
  text-align: center;
  margin: 20px 0;
}
</style>
