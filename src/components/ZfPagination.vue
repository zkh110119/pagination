<template>
  <ul class="zf-ui-pagination">
    <li>
      <el-select v-model="pageSize" :size="'mini'" style="width: 72px;" @change="emitEvent">
        <el-option v-for="size of pageSizes" :key="size" :label="size" :value="size"></el-option>
      </el-select>
    </li>
    <li>
      <el-link :underline="false" icon="el-icon-d-arrow-left" @click="first"></el-link>
    </li>
    <li>
      <el-link :underline="false" icon="el-icon-arrow-left" @click="prev"></el-link>
    </li>
    <li><span>第</span></li>
    <li>
      <el-input-number v-model="page" @blur="pageBlur" @keyup.enter.native="changePage" :min="1"
                       :max="totalPage" :size="'mini'" :controls="false" style="width: 48px;"></el-input-number>
    </li>
    <li>
      <span>/{{totalPage}}页</span>
    </li>
    <li>
      <el-link :underline="false" icon="el-icon-arrow-right" @click="next"></el-link>
    </li>
    <li>
      <el-link :underline="false" icon="el-icon-d-arrow-right" @click="last"></el-link>
    </li>
    <li>
      <el-link :underline="false" icon="el-icon-refresh-right" @click="emitEvent"></el-link>
    </li>
    <li v-show="total > 0" style="float: right;">显示{{startNum}}到{{endNum}}条，共{{total}}条</li>
  </ul>
</template>

<script>
export default {
  name: 'ZfPagination',
  props: {
    total: {
      type: Number,
      required: true,
      default: 0
    }
  },
  data () {
    return {
      page: 1,
      oldPage: 1,
      pageSizes: [20, 50, 100, 200],
      pageSize: 50
    }
  },
  watch: {
    totalPage (val, oldVal) {
      if (val && val !== oldVal) {
        if (this.page > val) {
          this.page = val
          this.oldPage = val
        }
      }
    }
  },
  methods: {
    first () {
      this.page = 1
      this.emitEvent()
    },
    prev () {
      if (this.page > 1) {
        this.page--
        this.emitEvent()
      }
    },
    next () {
      if (this.page < this.totalPage) {
        this.page++
        this.emitEvent()
      }
    },
    last () {
      this.page = this.totalPage
      this.emitEvent()
    },
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
    pageBlur () {
      this.page = this.oldPage
    },
    emitEvent () {
      this.$emit('changePageOrSize', {
        page: this.page,
        size: this.pageSize
      })
    }
  },
  computed: {
    totalPage () {
      return this.total === 0 ? 1 : Math.ceil(this.total / this.pageSize)
    },
    startNum () {
      return this.total === 0 ? 0 : this.page ? ((this.page - 1) * this.pageSize + 1) : ((this.oldPage - 1) * this.pageSize + 1)
    },
    endNum () {
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

      .el-link {
        width: 28px;
        height: 28px;
      }

      * {
        vertical-align: top;
      }
    }
  }
</style>
