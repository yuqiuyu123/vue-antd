<template>
  <div>
    <slot
      :list="list"
      :loading="loading"
      :total="pagination.total"
    >
    </slot>
    <div class="pagination">
      <a-pagination
        show-quick-jumper
        :current="pagination.current"
        :default-current="defaultCurrent"
        :disabled="disabled"
        :hideOnSinglePage="hideOnSinglePage"
        :itemRender="itemRender"
        :pageSize="pageSize"
        :pageSizeOptions="pageSizeOptions"
        :showLessItems="showLessItems"
        :showSizeChanger="showSizeChanger"
        :showTotal="showTotal"
        :simple="simple"
        :size="size"
        :total="pagination.total"
        @showSizeChange="onShowSizeChange"
        @change="paChange"
      />
    </div>
  </div>
</template>


<script>
import { debounce } from './index';

export default {
  name: 'PagedFetch',
  props: {
    requestMethod: {
      type: Function,
      default: () => { },
    },
    params: {
      type: Object,
    },
    mergePagination: {
      type: Function,
      default: (params, pagination) => {
        return {
          ...params,
          ...pagination
        }
      },
    },
    defaultCurrent: {
      type: Number,
      default: 1,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    hideOnSinglePage: {
      type: Boolean,
      default: false,
    },
    itemRender: {
      type: Function
    },
    pageSize: {
      type: Number,
      default: 10
    },
    pageSizeOptions: {
      type: Array,
      default: () => ['10', '20', '30', '40']
    },
    showLessItems: {
      type: Boolean,
      default: false
    },
    showSizeChanger: {
      type: Boolean,
      default: false
    },
    showTotal: {
      type: Function,
    },
    simple: {
      type: Boolean,
    },
    size: {
      type: String
    }

  },
  data () {
    return {
      list: [],
      loading: true,
      pagination: {
        current: 1,
        pageSize: 10,
        total: 0
      },

    };
  },

  computed: {
    fetchParams () {
      return { ...this.mergePagination(this.params, this.pagination) }
    },
  },

  watch: {
    params: {
      deep: true,
      handler () {
        this.pagination.current = 1;
        this.load()
      }
    },
  },

  created () {
    this.load()
  },

  methods: {

    load: debounce(async function () {
      this.list = await this.fetchData()
    }, 300),

    async fetchData () {
      this.loading = true
      const { data, count } = await this.requestMethod({
        ...this.fetchParams
      })

      if (count && count <= this.pagination.pageSize * (this.pagination.current - 1)) {
        this.$set(this.pagination, 'current', Math.ceil(count / this.pagination.pageSize));
        return await this.fetchData();
      }
      if (!count) {
        this.$set(this.pagination, 'current', 1);
      }
      this.$set(this.pagination, 'total', count);

      this.loading = false
      return data;
    },

    paChange (page, pageSize) {
      this.pagination.current = page
      this.load()
      this.$emit('change', page, pageSize)
    },

    onShowSizeChange (current, pageSize) {
      this.pagination.current = current
      this.pagination.pageSize = pageSize
      this.load()
      this.$emit('showSizeChange', current, pageSize)
    },
  }
}
</script>
<style scoped>
.pagination {
  text-align: right;
  margin-top: 10px;
}
</style>
