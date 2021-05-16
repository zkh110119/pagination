<template>
  <ul class="zf-ui-pagination">
    <li v-for="el in elements" :key="el" :class="{'pull-right':el==='info'}">
      <el-select
        v-if="el==='sizes'"
        class="zf-ui-pagination-page-size"
        v-model="pageSize"
        :size="'mini'"
        @change="changePageSize">
        <el-option
          v-for="size of pageList"
          :key="size"
          :label="size"
          :value="size"></el-option>
      </el-select>
      <el-link
        v-else-if="el==='first'"
        icon="el-icon-d-arrow-left"
        :underline="false"
        @click="first"></el-link>
      <el-link
        v-else-if="el==='prev'"
        icon="el-icon-arrow-left"
        :underline="false"
        @click="prev"></el-link>
      <slot v-else-if="el==='pagePrevText'" name="pagePrev">
        <span class="zf-ui-pagination-page-text-prev">
          第
        </span>
      </slot>
      <el-input-number
        v-else-if="el==='page'"
        v-model="page"
        @blur="pageBlur"
        @keyup.enter.native="changePage"
        :min="1"
        :max="pages"
        :size="'mini'"
        :controls="false"
        class="zf-ui-pagination-page"></el-input-number>
      <slot v-else-if="el==='pageNextText'" name="pageNext" :pages="pages">
        <span class="zf-ui-pagination-page-text-next">
          /{{pages}}页
        </span>
      </slot>
      <el-link
        v-else-if="el==='next'"
        icon="el-icon-arrow-right"
        :underline="false"
        @click="next">
      </el-link>
      <el-link
        v-else-if="el==='last'"
        icon="el-icon-d-arrow-right"
        :underline="false"
        @click="last"></el-link>
      <el-link
        v-else-if="el==='refresh'"
        icon="el-icon-refresh-right"
        :underline="false"
        @click="refresh"></el-link>
      <slot v-else-if="el==='info'" name="info" :from="from" :to="to" :total="total" v-show="total>0">
        <span>显示{{from}}到{{to}}条，共{{total}}条</span>
      </slot>
      <span v-else></span>
    </li>
  </ul>
</template>

<script>
export default {
  name: 'ZfPagination',
  props: {
    // 数据总条数
    total: {
      type: Number,
      required: true,
      default: 0
    },
    // 每页展示多少条数据的集合
    list: {
      type: Array,
      default: function () {
        return [20, 50, 100, 200]
      }
    },
    // 每页展示多少数据
    size: {
      type: Number,
      default: 50
    },
    // 元素展示顺序
    els: {
      type: Array,
      default: function () {
        return ['sizes', 'first', 'prev', 'pagePrevText', 'page', 'pageNextText', 'next', 'last', 'refresh', 'info']
      }
    }
  },
  data () {
    return {
      page: 1, // 页面数
      oldPage: 1, // 记录页码数
      pageSize: this.size,
      pageList: [...this.list],
      elements: [...this.els]
    }
  },
  watch: {
    /**
       * 监听总页数数值变化
       * @param val 新的pages
       * @param oldVal 变化前的pages
       */
    pages (val, oldVal) {
      if (val && val !== oldVal) {
        // 如果当前页面数大于总页码数，将当前页面数修改为总页码数
        if (this.page > val) {
          this.page = val
          this.oldPage = val
        }
      }
    }
  },
  methods: {
    /**
       * 跳转到首页，并触发分页数据变化事件
       */
    first () {
      this.page = 1
      this.emitEvent()
    },
    /**
       * 跳转到上一页，并触发分页数据变化事件
       */
    prev () {
      if (this.page > 1) {
        this.page--
        this.emitEvent()
      }
    },
    /**
       * 跳转到下一页，并触发分页数据变化事件
       */
    next () {
      if (this.page < this.pages) {
        this.page++
        this.emitEvent()
      }
    },
    /**
       * 跳转到末尾页，并触发分页数据变化事件
       */
    last () {
      this.page = this.pages
      this.emitEvent()
    },
    /**
       * 刷新事件
       */
    refresh () {
      this.emitEvent()
    },
    /**
       * 页码数输入框回车事件
       */
    changePage () {
      if (this.page) {
        if (this.page !== this.oldPage) {
          this.emitEvent()
        }
        this.oldPage = this.page
      } else {
        this.page = this.oldPage
      }
    },
    /**
       * 每页展示条数变化时触发事件
       */
    changePageSize () {
      // 重新计算当前页码数是否大于总页码数，防止watch监听还没从触发就调用分页数据变化通知方法，导致page错误
      if (this.page > this.pages) {
        this.page = this.pages
        this.oldPage = this.pages
      }
      this.emitEvent()
    },
    /**
       * 当前页码数输入框失去焦点时触发事件
       */
    pageBlur () {
      this.page = this.oldPage
    },
    /**
       * 分页数据变化通知方法
       */
    emitEvent () {
      this.$emit('pageOrSizeChange', {
        page: this.page,
        size: this.pageSize
      })
    }
  },
  computed: {
    /**
       * 计算总页码数
       * @returns {number}
       */
    pages () {
      return this.total === 0 ? 1 : Math.ceil(this.total / this.pageSize)
    },
    /**
       * 计算当前页开始条数
       * @returns {number}
       */
    from () {
      return this.total === 0 ? 0 : this.page ? ((this.page - 1) * this.pageSize + 1) : ((this.oldPage - 1) * this.pageSize + 1)
    },
    /**
       * 计算当前页结束条数
       * @returns {number}
       */
    to () {
      return this.total === 0 ? 0 : this.page ? this.page * this.pageSize < this.total ? this.page * this.pageSize : this.total : this.oldPage * this.pageSize
    }
  }
}
</script>

<style lang="scss" scoped>
  .zf-ui-pagination {
    position: relative;
    width: 100%;
    height: 28px;
    list-style: none;
    margin: 0;
    padding: 0;
    text-align: left;

    li {
      display: inline-block;
      height: 100%;
      line-height: 28px;
      font-size: 14px;

      &.pull-right {
        float: right;
      }

      .el-link {
        width: 28px;
        height: 28px;
      }

      * {
        vertical-align: top;
      }

      .zf-ui-pagination-page-size {
        width: 72px;
      }

      .zf-ui-pagination-page {
        width: 48px;
      }

      .zf-ui-pagination-page-text-prev {
        padding: 0 4px;
      }

      .zf-ui-pagination-page-text-next {
        padding: 0 4px;
      }
    }
  }
</style>
