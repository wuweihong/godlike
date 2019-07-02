<template>
  <div class="app-container">
    <div class="table-main">
    <div class="arrR" v-show="arrows"> <i class="el-icon-d-arrow-left"></i> </div>
    <div class="arrL" v-show="arrows"> <i class="el-icon-d-arrow-right"></i> </div>
    <el-table :data="list" v-loading="listLoading" 
      style="width:100%"
      class="table-container"
      @mousedown.native="dragDowmX($event)"
      @mousemove.native="dragMoveX($event)"
      @mouseup.native="dragUpX($event)"
      @mouseleave.native="dragLeaveX($event)"
      @click.native="dragClickX($event)"
      element-loading-text="Loading" border fit highlight-current-row>
      <el-table-column align="center" label='ID' width="95" fixed>
        <template slot-scope="scope">
          {{scope.$index}}
        </template>
      </el-table-column>
      <el-table-column label="Title" width="1310">
        <template slot-scope="scope">
          {{scope.row.title}}
        </template>
      </el-table-column>
      <el-table-column label="Author" width="110" align="center" >
        <template slot-scope="scope">
          <span>{{scope.row.author}}</span>
        </template>
      </el-table-column>
      <el-table-column label="Pageviews" width="110" align="center" >
        <template slot-scope="scope">
          {{scope.row.pageviews}}
        </template>
      </el-table-column>
      <el-table-column class-name="status-col" label="Status" width="110" align="center">
        <template slot-scope="scope">
          <el-tag :type="scope.row.status | statusFilter">{{scope.row.status}}</el-tag>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="Display_time" width="200">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span>{{scope.row.display_time}}</span>
        </template>
      </el-table-column>
    </el-table>
    </div>

  </div>
</template>

<script>
import { getList } from '@/api/table'
export default {
  data() {
    return {
      list: null,
      listLoading: true,
      start: false,
      arrows: false,
      gapX: 0, // 记录鼠标初始位置
      clickTime: null, // 定时器方法
      lastMove: 0 // 鼠标放开后的距离
    }
  },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  created() {
    this.fetchData()
  },

  mounted() {
    // 固定列动态修改固定列高度
    window.onresize = () => {
      return (() => {
        window.screenWidth = document.body.clientWidth
        this.tableHeight = window.innerHeight - 165
        if (this.widowHeight !== window.innerHeight) {
          this.handleSuitTableHigh()
        }
      })()
    }
    setTimeout(() => {
      this.handleSuitTableHigh()
    }, 1000)
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getList(this.listQuery).then(response => {
        this.list = response.data.items
        this.listLoading = false
      })
    },
    // 处理table固定高度
    handleSuitTableHigh() {
      this.widowHeight = window.innerHeight
      const tables = document.getElementsByClassName('el-table__fixed')
      for (const item of tables) {
        setTimeout(() => {
          item.style.height = parseInt(item.style.height) + 17 + 'px'
        }, 100)
        const wrappers = item.getElementsByClassName('el-table__fixed-body-wrapper')
        for (const wrap of wrappers) {
          setTimeout(() => {
            wrap.style.height = parseInt(wrap.style.height) + 17 + 'px'
          }, 100)
        }
      }
    },
    /* eslint-disable */
    // 鼠标按下
    dragDowmX(ev) {
      if (ev.button == 0) {
        this.gapX = ev.clientX // 记录开始的鼠标
        this.clickTime = setTimeout(() => {
          $('.arrR').css('top', `${ev.pageY - 70}px `)
          $('.arrL').css('top', `${ev.pageY - 70}px`)
          this.start = true
          this.arrows = true
          $('.table-container').css('user-select', 'none')
        }, 500)
      }
    },
    // 鼠标移动
    dragMoveX(ev) {
      if (this.start) {
        let moveX = ev.clientX - this.gapX + this.lastMove
        const el = $('.el-table__header-wrapper')[0]
        // 当没有超出时,不移动
        if (el.clientWidth >= el.scrollWidth) return
        this.arrows = false
        if (moveX > 0) {
        // 判断是否滑到最右边
          if (moveX >= el.scrollWidth - el.clientWidth) moveX = el.scrollWidth - el.clientWidth
          $('.el-table__header-wrapper').css('transform', `translateX(-${moveX}px)`)
          $('.el-table__body-wrapper').css('transform', `translateX(-${moveX}px)`)
          $('.el-table__header-wrapper').css('transition', '0.1s')
          $('.el-table__body-wrapper').css('transition', '0.1s')
          this.lastMove = moveX
        } else {
          $('.el-table__header-wrapper').css('transition', '0.1s')
          $('.el-table__body-wrapper').css('transition', '0.1s')
          $('.el-table__header-wrapper').css('transform', 'translateX(0)')
          $('.el-table__body-wrapper').css('transform', 'translateX(0)')
          this.lastMove = 0
        }
      }
    },
    // 鼠标松开
    dragUpX() {
      clearTimeout(this.clickTime)
      this.start = false
      this.arrows = false
      $('.table-container').css('user-select', 'text')
    },
    // 鼠标点击
    dragClickX() {
      clearTimeout(this.clickTime)
      $('.table-container').css('user-select', 'text')
      this.start = false
      this.arrows = false
    },
    // 鼠标离开
    dragLeaveX() {
      clearTimeout(this.clickTime)
      $('.table-container').css('user-select', 'text')
      this.start = false
      this.arrows = false
    }
  }
}
</script>
<style scoped>
.table-main {
  width: 100%;
  position: relative;
}
.arrR {
  font-size: 40px;
  font-weight: 700;
  position: absolute;
  left: 40px;
  z-index: 9999;
  color: rgb(88, 88, 88);
  -webkit-animation: mymove 1.5s infinite linear;
}
.arrL {
  font-weight: 700;
  font-size: 40px;
  position: absolute;
  right: 40px;
  z-index: 9999;
  color: rgb(88, 88, 88);
  -webkit-animation: mymove 1.5s infinite linear;
}
@keyframes mymove
{
0%   {-webkit-transform:scale(1) rotateX(0deg);}
25%  {-webkit-transform:scale(1.2)  rotateX(90deg);}
50%  {-webkit-transform:scale(1.5) rotateX(180deg);}
75%  {-webkit-transform:scale(1.2)  rotateX(270deg);}
100% {-webkit-transform:scale(1)  rotateX(360deg);}
}
.table-container {
  width: 100%;
}
</style>

<style>
/* .el-table__header-wrapper{
  width: 100% !important;
  overflow: visible;
}
.el-table__body-wrapper{
  width: 100% !important;
  overflow: visible;
}
.el-table--scrollable-x .el-table__body-wrapper {
  overflow-x: visible;
} */
</style>

