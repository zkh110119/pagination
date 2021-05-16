<template>
  <div class="about">
    <el-container>
      <el-main>
        <el-input-number v-model="total"></el-input-number>
      </el-main>
      <el-footer>
        <zf-pagination :total="total" @pageOrSizeChange="pageOrSizeChange">
          <span slot="info" slot-scope="scope">当前页显示{{scope.from}}-{{scope.to}}条，总共{{scope.total}}条数据。</span>
        </zf-pagination>
      </el-footer>
    </el-container>
  </div>
</template>
<script>
import ZfPagination from '../components/ZfPagination'

export default {
  components: { ZfPagination },
  data () {
    return {
      total: 320
    }
  },
  methods: {
    pageOrSizeChange (data) {
      console.log(data)
    }
  }
}
</script>
<style lang="scss" scoped>
  .about {
    width: 100%;
    height: 100%;
  }
</style>
