<template>
  <div class="chart-container" >
    <!-- 全屏 -->
    <div v-show="isFull" class="full-container" id="bar-full" style="border:1px solid red;padding:20px">
      <div>
        <el-button  @click="fullScreen">关闭</el-button>
      </div>
      <chart height="100%" width="100%" 
        className="barCharts" id="bar-full"
        :themeType="themeType"/>
    </div>

    <div style="margin:20px 0" v-show="!isFull">
      <el-row :gutter="20">
        <el-col :span="8">
          <div>
            <span>主题切换: </span>
            <el-select v-model="value" placeholder="请选择" @change="themeChange">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </div>
        </el-col>
        <el-col :span="6">
          点击全屏:
          <el-button type="text" @click="fullScreen">全屏展示</el-button>
        </el-col>
      </el-row>
    </div>

    <chart height="100%" width="100%" 
    className="barCharts" id="bar"
    :themeType="themeType"/>

  </div>
</template>
<script>
import Chart from './bar'
import themeName from '../themeName'
export default {
  name: 'barCharts',
  components: { Chart },
  data() {
    return {
      themeType: 'macarons',
      options: themeName, // 主题名称
      value: 'macarons',
      isFull: false // 全屏标识
    }
  },
  methods: {
    themeChange(val) {
      this.themeType = val
    },
    fullScreen() {
      this.isFull = true
    }
  }
}
</script>
<style lang="scss" scoped>
.chart-container{
  position: relative;
  width: 100%;
  height: calc(100vh - 150px);
}
.full-container {
  position: relative;
  width: calc(100vw - 250px);
  height: 100vh;
  z-index: 9999;
  background-color: #fff;
}
</style>

