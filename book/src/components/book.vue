<template>
  <a-row type="flex" justify="space-around" align="middle">
<!--      style="text-align: left;font-weight: bold;margin-left: 15px"-->
      <a-col :span="2"><a-typography-title :level="2">{{year}}<SmileTwoTone twoToneColor="#002FA7" style="margin-left: 15px"/></a-typography-title></a-col>
      <a-col :span="1" style="margin-bottom: 15px">
        <a-dropdown>
          <template #overlay>
            <a-menu @click="handleMenuClick">
              <a-menu-item key="2021" v-if="year=='2021'?0:1">2021</a-menu-item>
              <a-menu-item key="2022" v-if="year=='2022'?0:1">2022</a-menu-item>
            </a-menu>
          </template>
          <a-button>
            年份选择
            <DownOutlined />
          </a-button>
        </a-dropdown>
      </a-col>
    <a-col :span="18"></a-col>
    <a-col :span="2"><a-button type="link" @click="showDrawer">我的图书报告</a-button></a-col>
    <a-col :span="1"></a-col>
  </a-row>
  <a-table :columns="columns" :data-source="year=='2021'?data2021:data2022" :pagination="false" rowKey="key">

    <template #bodyCell="{ column, record }">
      <template v-if="column.key === 'name'">
        <a>
          <a-popover :title="record.name" placement="bottom" @mouseenter="getDetails(record.ISBN)">
            <template #content >
              <p v-if="isLoading">加载中...</p>
              <img :src="'https://images.weserv.nl/?url='+photoUrl" style="width: 81px;height: 117px;float: left" alt="图片加载中...">
              <div style="float: left;width: 250px;padding-left: 10px" >{{bookDetail}}</div>
              <div style="clear:both"></div>
            </template>
            {{ record.name }}
          </a-popover>
        </a>
      </template>
      <template v-else-if="column.key === 'tags'">
        <span>
          <a-tag
              v-for="tag in record.tags"
              :key="tag"
              :color=color(tag)
          >
            {{tag}}
          </a-tag>
        </span>
      </template>
<!--      <template v-else-if="column.key === 'action'">-->
<!--        <span>-->
<!--          <a>Invite 一 {{ record.name }}</a>-->
<!--          <a-divider type="vertical" />-->
<!--          <a>Delete</a>-->
<!--          <a-divider type="vertical" />-->
<!--          <a class="ant-dropdown-link">-->
<!--            More actions-->
<!--            <down-outlined />-->
<!--          </a>-->
<!--        </span>-->
<!--      </template>-->
    </template>
  </a-table>
  <a-drawer width="640" placement="right" :closable="false" :visible="visible" @close="onClose">
    <p :style="[pStyle, pStyle2]">我的{{year}}</p>
    <p :style="pStyle">Personal</p>
    <a-row>
      <a-col :span="12">
      </a-col>
      <a-col :span="12">
      </a-col>
    </a-row>
  </a-drawer>
</template>
<script>

import { DownOutlined,SmileTwoTone } from '@ant-design/icons-vue';
import { message } from 'ant-design-vue';
import {defineComponent, ref, watch} from 'vue';
import axios from 'axios'
import booklist2021 from '../assets/book2021'
import booklist2022 from '../assets/book2022'
const columns = [
  {
    title:"排名",
    dataIndex: "index",
    key:"index",
    customRender:(text)=> {
      return parseInt(text.index)+1
    }
  },
  {
    title:'书名',
    name: 'Name',
    dataIndex: 'name',
    key: 'name',
  },
  {
    title: '作者',
    dataIndex: 'editor',
    key: 'editor',
  },
  {
    title: '标签',
    key: 'tags',
    dataIndex: 'tags',
  },
  {
    title: '评分',
    key: 'rate',
    dataIndex:'rate',
    customRender:(text)=> {
      const rate=parseFloat(text.value)
      let x=parseInt(rate)
      let string=""
      for(let i=0;i<x;i++){
        string+="🌕"
      }
      let y=rate%1
      if(y==0.5){
        string+="🌗"
      }else if(y<0.5&&y>0){
        string+="🌘"
      }else if(y>0.5){
        string+="🌖"
      }
      if(string.length<10){
        for(let i=string.length;i<10;i+=2){
          string+="🌑"
        }
      }
      return (string)
    },
    defaultSortOrder: 'descend',
    sorter: (a, b) => a.rate - b.rate,
  },
];

const data2021 = booklist2021
const data2022 = booklist2022

export default defineComponent({
  components: {
    DownOutlined,
    SmileTwoTone,
  },
  setup() {
    let year=ref("2021")
    let bookDetail=ref("")
    let isLoading=ref(true)
    let photoUrl=ref("")
    //https://jike.xyz/jiekou/isbn.html#%E8%BF%94%E5%9B%9E%E5%8F%82%E6%95%B0%E8%AF%B4%E6%98%8E
    //私人接口APIKEY，如无法获得数据，需手动更改
    const apikey="13294.206eaa07ed2ea8b374cf076241a2024c.f57bd57f2303f9557c040b0365744e1c"
    const visible = ref(false);
    const pStyle = {
      fontSize: '16px',
      color: 'rgba(0,0,0,0.85)',
      lineHeight: '24px',
      display: 'block',
      marginBottom: '16px',
    };
    const pStyle2 = {
      marginBottom: '24px',
    };

    const showDrawer = () => {
      visible.value = true;
    };
    const onClose = () => {
      visible.value = false;
    };
    const success = () => {
      message.success('This is a success message');
    };
    function handleMenuClick(e){
      year.value=e.key
    }
    function getDetails(ISBN){
      isLoading.value=true
      bookDetail.value=""
      photoUrl.value=""
      axios.get(`https://api.jike.xyz/situ/book/isbn/${ISBN}?apikey=${apikey}`)
          .then(res=>{
            isLoading.value=false
            if(res.data.data.description.length>100) {
              bookDetail.value = res.data.data.description.slice(0,100)+"..."
            }
            photoUrl.value=res.data.data.photoUrl
          }).catch(err=>{
        console.log(err);
      })
    }
    const color=(name)=>{
      switch (name){
        case "小说":
          return 'blue'
        case "推理":
          return 'purple'
        case "历史":
          return 'gold'
        case "纪实":
          return 'orange'
        case "非纪实":
          return 'cyan'
        case "社会":
          return 'lime'
        case "科幻":
          return 'red'
        case "哲学":
          return 'pink'
        case "非小说":
          return 'green'
        default:
          return 'default'

      }
    }
    watch(year,(n,o)=>{
      message.success("已为你切换至"+n+"年")
    })
    return {
      handleMenuClick,
      getDetails,
      visible,
      pStyle,
      pStyle2,
      showDrawer,
      onClose,
      success,
      data2021,
      data2022,
      isLoading,
      bookDetail,
      photoUrl,
      year,
      columns,
      color,
    };
  },
});
</script>
