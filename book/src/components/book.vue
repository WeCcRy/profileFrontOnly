<template>
  <h1 style="text-align: left;font-weight: bold;margin-left: 15px">2022</h1>
  <a-table :columns="columns" :data-source="data" :pagination="false" rowKey="key">

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
import { defineComponent } from 'vue';
import booklist from '../assets/book'
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
    dataIndex:'rate'
  },
];

const data = booklist

export default defineComponent({
  components: {
    DownOutlined,
  },
  setup() {
    const color=(name)=>{
      switch (name){
        case "小说":
          return 'blue'
          break;
        case "推理":
          return 'purple'
          break;
        case "历史":
          return 'gold'
          break;
        case "纪实":
          return 'orange'
          break;
        case "非纪实":
          return 'cyan'
          break;
        case "社会":
          return 'lime'
          break;
        case "科幻":
          return 'red'
          break;
        case "哲学":
          return 'pink'
          break;
        case "博物科普":
          return 'green'
          break;
        default:
          return 'default'

      }
    }
    return {
      data,
      columns,
      color,
    };
  },
});
</script>