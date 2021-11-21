<template>
  <div id="app">
    <div style="margin-bottom: 10px">
      <a-row>
        <a-col :span="1">
          <a-button @click="search">刷新</a-button>
        </a-col>
        <a-col :span="10">
          <a-input-search
            placeholder="input search text"
            v-model="params.name"
          />
        </a-col>
      </a-row>
    </div>

    <paged-fetch
      :requestMethod="fetchData"
      :params="params"
      ref="pagedFetch"
      :mergePagination="mergePagination"
      :show-size-changer="true"
    >
      <template v-slot:default="{list, loading, total}">
        <div>共计: {{total? total: 0}}</div>
        <a-table
          :columns="columns"
          :data-source="list"
          :pagination="false"
          :loading="loading"
          :row-selection="{
          onSelectAll: onSelectAll
        }"
        >
          <a
            slot="name"
            slot-scope="text"
          >{{ text }}</a>
          <span slot="customTitle">
            <a-icon type="smile-o" /> Name
          </span>
          <span
            slot="tags"
            slot-scope="tags"
          >
            <a-tag
              v-for="tag in tags"
              :key="tag"
              :color="tag === 'loser' ? 'volcano' : tag.length > 5 ? 'geekblue' : 'green'"
            >
              {{ tag.toUpperCase() }}
            </a-tag>
          </span>
          <span
            slot="action"
            slot-scope="text, record"
          >
            <a>Invite 一 {{ record.name }}</a>
            <a-divider type="vertical" />
            <a>Delete</a>
            <a-divider type="vertical" />
            <a class="ant-dropdown-link"> More actions
              <a-icon type="down" />
            </a>
          </span>
        </a-table>
      </template>
    </paged-fetch>
  </div>
</template>

<script>
import PagedFetch from './components/PageFetch.vue';

const columns = [
  {
    dataIndex: 'name',
    key: 'name',
    slots: { title: 'customTitle' },
    scopedSlots: { customRender: 'name' },
  },
  {
    title: 'Age',
    dataIndex: 'age',
    key: 'age',
  },
  {
    title: 'Address',
    dataIndex: 'address',
    key: 'address',
  },
  {
    title: 'Tags',
    key: 'tags',
    dataIndex: 'tags',
    scopedSlots: { customRender: 'tags' },
  },
  {
    title: 'Action',
    key: 'action',
    scopedSlots: { customRender: 'action' },
  },
];

export default {
  name: 'App',
  components: {
    PagedFetch
  },
  data () {
    return {
      columns,
      params: {
        name: ''
      }
    }
  },
  methods: {
    // 这里mock 实际是请求接口
    fetchData () {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve({
            data: [
              {
                key: '1',
                name: 'John Brown',
                age: 32,
                address: 'New York No. 1 Lake Park',
                tags: ['nice', 'developer'],
              },
              {
                key: '2',
                name: 'Jim Green',
                age: 42,
                address: 'London No. 1 Lake Park',
                tags: ['loser'],
              },
              {
                key: '3',
                name: 'Joe Black',
                age: 32,
                address: 'Sidney No. 1 Lake Park',
                tags: ['cool', 'teacher'],
              },
            ],
            count: 100
          })
        }, 100);
      })
    },
    onSelectAll (selected, selectedRows, changeRows) {
      console.log(selected, selectedRows, changeRows);
    },
    search () {
      this.$refs.pagedFetch.load()
    },
    mergePagination (params, pagination) {
      return {
        params,
        pagination
      }
    },
  }

}
</script>

<style>
#app {
  padding: 40px;
}
</style>
