<template>
  <a-typography-title style="text-align: center">
    抖音主播信息查询
  </a-typography-title>
  <a-typography-title :heading="6" style="text-align: center" type="secondary">
    支持10w+直播间具体人数查询
  </a-typography-title>
  <div class="main">

    <a-form :model="form"  @submit="handleSubmit" layout="vertical">
      <a-form-item field="name" label="主播昵称" tooltip="要保证第一个搜到的是他">
        <a-input
            v-model="form.nickname"
            placeholder=""
        />
      </a-form-item>
      <a-form-item disabled field="post" label="主播id">
        <a-input v-model="form.web_rid" placeholder="暂不支持"/>
      </a-form-item>

      <a-form-item>
        <a-button html-type="submit">查询</a-button>
      </a-form-item>
    </a-form>
    <a-card :loading="card_data.is_load"  >
      <template #actions>
        <span class="icon-hover"> <IconThumbUp/> </span>
        <span class="icon-hover"> <IconShareInternal/> </span>
        <span class="icon-hover"> <IconMore/> </span>
      </template>
      <a-card-meta  :description="'在线观众 : '+card_data.display_value"
                   :title="card_data.title">
        <template #avatar>
          <div
              :style="{ display: 'flex', alignItems: 'center', color: '#1D2129' }"
          >
            <a-avatar :size="60" :style="{ marginRight: '8px' }" v-show="card_data.display_value!==0">
              <img
                  :src="card_data.avatar"
                  :style="{ width: '100%' }"
                  alt="dessert"
              />
            </a-avatar>

            <a-typography-text>{{ card_data.nickname }}</a-typography-text>
          </div>
        </template>
      </a-card-meta>
    </a-card>

    <!--    <a-typography-title :heading="3" style="text-align: center">-->
    <!--      相关主播-->
    <!--    </a-typography-title>-->
  </div>
</template>

<script>
import {IconMore, IconShareInternal, IconThumbUp,} from '@arco-design/web-vue/es/icon';
import {reactive} from "vue";
import axios from 'axios';


export default {
  setup() {
    const form = reactive({
      nickname: '',
      web_rid: '',
      isRead: false,
    });

    const card_data = reactive({
      "avatar": "",
      "display_value": 0,
      "nickname": "",
      "title": "",
      "web_rid": "",
      "is_load": false
    });

    const handleSubmit = async () => {
      console.log('click')
      try {
        card_data.is_load = true;
        const response = await axios.post(import.meta.env.VITE_BASE_URL, form);
        console.log(response.data); // 如果需要处理返回的数据，请将其打印出来或按需处理
        card_data.avatar = response.data.avatar;
        card_data.display_value = response.data.display_value;
        card_data.nickname = response.data.nickname;
        card_data.title = response.data.title;
        card_data.web_rid = response.data.web_rid;
      } catch (error) {
        console.error('Error:', error);
      } finally {
        card_data.is_load = false;
      }
    };

// 调用 handleSubmit 函数并将表单数据作为参数传递
//     handleSubmit(form);

    return {
      form,
      handleSubmit,
      card_data
    };
  },
  components: {IconThumbUp, IconShareInternal, IconMore},
};
</script>
<style>
.arco-form-item-content-flex{
  justify-content: flex-end;
}
</style>
