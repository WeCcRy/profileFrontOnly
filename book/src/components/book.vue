<template>
  <h1 style="text-align: left;font-weight: bold;margin-left: 15px">
    <span style="padding-right: 10px">{{year}}</span>
    <a-dropdown>
    <template #overlay>
      <a-menu @click="handleMenuClick">
        <a-menu-item key="2021">2021</a-menu-item>
        <a-menu-item key="2022">2022</a-menu-item>
      </a-menu>
    </template>
    <a-button>
      年份选择
      <DownOutlined />
    </a-button>
  </a-dropdown>
  </h1>
  <a-table :columns="columns" :data-source="year=='2021'?data2021:data2022" :pagination="false" rowKey="key">

    <template #bodyCell="{ column, record }">
      <template v-if="column.key === 'name'">
        <a>
          {{ record.name }}
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
</template>
<script>
import { DownOutlined } from '@ant-design/icons-vue';
import { defineComponent,ref } from 'vue';
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
    }
  },
];

const data2021 = booklist2021
const data2022 = booklist2022

export default defineComponent({
  components: {
    DownOutlined,
  },
  setup() {
    let year=ref("2021")
    function handleMenuClick(e){
      year.value=e.key
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
    return {
      handleMenuClick,
      data2021,
      data2022,
      year,
      columns,
      color,
    };
  },
});
</script>
