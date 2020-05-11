<template>
  <div>
    <!-- 表格搜索 -->
    <div class="search">
      <!-- <div class="wrapp">
        <span>姓名：</span>
         <input
          v-model="query.name"
          type="text"
          placeholder="请输入姓名" />
      </div> -->
      <div class="wrapp"
        v-for="(query) of querydata"
        :key="query.parameter">
        <span>{{query.text}}：</span>
        <input
          v-model="querySelect[query.parameter]"
          type="text"
          placeholder="请输入值" />
      </div>
      <div class="range"
        v-for="(range,index) of ranges"
        :key="index"
      >
        <span>{{range.name}}：</span>
        <div>
           <span>最小值</span>
           <input
              v-model="range.low"
              type="text"
              placeholder="请输入最小值" />
             <span style="margin-left:20px;">最大值</span>
             <input
              v-model="range.high"
              type="text"
              placeholder="请输入最大值" />
        </div>
      </div>
    </div>
  <!-- 表格 -->
  <div class="table">
    <table class="responsive-table">
    <!-- 表格头部 -->
    <thead>
      <tr>
        <th
          v-for="(thead,index) of theaddata"
          :key="index"
          @click="thead.isSort && sortByName(thead.sortName)">{{thead.name}}</th>
        <!-- <th
        @click="sort = {key: 'ranking', val: -sort.val}">排名</th> -->
      </tr>
    </thead>
    <tbody v-if="players.length">
      <tr
      v-for="player of players"
      :key="player.id"
      :class="player.factionId? 'horde':'alliance'">
        <th>{{ player.ranking }}</th>
        <th>{{ player.rating }}</th>
        <th>
          {{ player.name }}
        </th>
        <th>{{ player.weeklyRate }}</th>
        <th>
          <img class="img" v-if="player.image" :src="player.image" alt="">
        </th>
      </tr>
    </tbody>
    <tbody v-else>
      <p class="nodata">暂无数据</p>
    </tbody>
    </table>
  </div>

  <!-- 分页 -->
  <div class="pagination">
    <p class="pagination-wrapp">
      <select v-model="limit">
        <option value="0" disabled selected>请选择每页的条数</option>
        <option
        v-for="(item,index) in limitdata"
        :key="index"
        :value="item">{{item }}</option>
      </select>
      <a class="button" @click="changePage(-1)">上一页</a>
      当前第<input v-model="page" type="text" class="page">页 共{{ total }}页
      <a class="button" @click="changePage(1)">下一页</a>
    </p>
  </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      showNoData: false,
      // 搜索查询条件参数
      querySelect: {
        // name: '',
        // rating: null,
        // weeklyRate: null
      },
      // 排序
      sort: {
        key: 'ranking',
        val: 1
      },
      // 分页参数
      page: 1,
      limit: 10,
      total: 0
    }
  },
  props: {
    querydata: {
      type: Array,
      default: () => {
        return []
      }
    },
    ranges: {
      type: Array,
      default: () => {
        return []
      }
    },
    theaddata: {
      type: Array,
      required: true,
      default: () => {
        return []
      }
    },
    tabledata: {
      type: Array,
      required: true,
      default: () => {
        return []
      }
    },
    limitdata: {
      type: Array,
      default: () => {
        return [3, 5, 10, 20]
      }
    }
  },
  computed: {
    players () {
      let arr = []
      arr = this.queryFilter(this.querySelect, this.tabledata)
      console.log('执行后的arr', arr)
      arr = this.ratingFilter(this.ranges, arr)
      this.sortTable(arr)
      arr = this.paginate(arr)
      console.log('最终的arr', arr)
      // this.showNoData = !arr.length
      return arr
    }
  },
  created () {
    console.log('tabledata--', this.tabledata)
    // const list = {}
    console.log('this.querydata', this.querydata)
    this.querydata.forEach((value) => {
      this.$set(this.querySelect, value.parameter, '')
    })
    console.log(22222, this.querySelect)
  },
  methods: {
    queryFilter (condition, arr) {
      console.log('condition', condition)
      // 拿到有值的参数
      const tempFilter = {}
      for (const choose in condition) {
        if (typeof (condition[choose]) !== 'undefined' && condition[choose] !== null && condition[choose] !== '') {
          tempFilter[choose] = condition[choose]
        }
      }
      console.log(111, tempFilter)
      // 拿到有值的参数转成数组
      const tempFilterArr = Object.keys(tempFilter)
      console.log(222, tempFilterArr)
      // 能否成功转成数字
      tempFilterArr.map(item => {
        const n = Number(condition[item])
        if (!isNaN(n)) { // 数字
          condition[item] = n
        }
      })
      console.log('condition--转数字', condition)
      // 筛选
      arr = arr.filter((item) => {
        return tempFilterArr.every(key => {
          return condition[key] === item[key]
        })
      })
      console.log('arr--', arr)
      return arr
    },
    ratingFilter (ranges, arr) {
      // console.log('ranges--', ranges)
      if (ranges.length === 0) {
        return arr
      } else {
        for (const range of ranges) {
          // 多个不同类型区间是与逻辑，可以直接赋值给自身
          arr = arr.filter(function (item) {
            return item[range.type] >= range.low && item[range.type] <= range.high
          })
        }
        return arr
      }
    },
    sortByName (name) {
      this.sort = { key: name, val: -this.sort.val }
    },
    // 排序表格
    sortTable (arr) {
      let n = 0
      arr.sort((a, b) => {
        n++
        if (a[this.sort.key] > b[this.sort.key]) {
          return this.sort.val
        } else {
          if (a[this.sort.key] < b[this.sort.key]) {
            return -this.sort.val
          }
        }
      })
    },
    paginate (arr) {
      this.total = Math.ceil(arr.length / this.limit)
      let page = parseInt(this.page - 1)
      if (page < 0) {
        page = 0
      }
      arr = arr.slice(this.limit * page, this.limit * (page + 1))
      return arr
    },
    changePage (num) {
      if (num === 1) {
        if (this.page < this.total) {
          this.page++
        } else {
          window.alert('已是最后一页')
        }
      } else {
        if (this.page > 1) {
          this.page--
        } else {
          window.alert('已是第一页')
        }
      }
    }
  }
}
</script>
<style scoped lang="scss">
  .pagination {
    width: 100%;
    height: 50px;
    line-height: 50px;
    text-align: center;
    display: flex;
    justify-content: flex-end;
    &-wrapp {
      margin-right: 100px;
    }
  }
  .page {
   min-width: 20px;
   max-width: 80px;
   height: 30px;
   line-height: 30px;
   width:auto;
   padding:10px;
   text-align: center;
  }
  .search {
    width: 100%;
    height: 50px;
    display: flex;
    flex-wrap: wrap;
  }
  .wrapp {
    min-width: 300px;
    width:auto;
    margin-left: 20px;
    font-size: 16px;
    height: 60px;
    line-height: 60px;
  }
  .wrapp input, .range input {
    min-width: 100px;
    height: 30px;
    line-height: 30px;
    margin-left:10px;
    font-size: 16px;
    border: 1px solid #dedede;
    border-radius: 8px;
    padding-left:20px;
  }
  .range {
    min-width: 200px;
    height: 60px;
    display: flex;
    align-items: center;
    margin-left: 20px;
  }
  .table {
    width: 100%;
    margin-top:50px;
  }
  th {
    font-weight: normal;
    min-width: 250px;
    height: 50px;
    line-height: 50px;
    width:auto;
  }
  th:last-child, td:last-child {
    padding-right: 1.5rem;
  }
  th:first-child, td:first-child {
    padding-left: 1.5rem;
  }
  .horde {
    background: rgba(255, 205, 210, .4);
  }
  .alliance {
    background: rgba(179, 229, 252, .4);
  }
  .img {
    width: 100px;
    height: 100px;
  }
  .nodata{
    text-align:center;
    width: 100%;
    // min-width:300px !important;
    height: 80px;
    line-height: 80px;
  }
  @media only screen and (max-width: 992px) {
    th, td {
      border-bottom: 0;
    }
    tbody tr {
      border-right: 1px solid rgba(208, 208, 208, .5);
    }
    table.responsive-table {
      min-width: 100%;
      border-collapse: collapse;
      border-spacing: 0;
      display: block;
      position: relative;
    }
    table.responsive-table td:empty:before {
      content: '\00a0';
    }
    table.responsive-table th,
    table.responsive-table td {
      margin: 0;
      vertical-align: top;
    }
    table.responsive-table th {
      text-align: left;
    }
    table.responsive-table thead {
      display: block;
      float: left;
    }
    table.responsive-table thead tr {
      display: block;
      padding: 0 10px 0 0;
    }
    table.responsive-table thead tr th::before {
      content: "\00a0";
    }
    table.responsive-table tbody {
      display: block;
      width: auto;
      position: relative;
      overflow-x: auto;
      white-space: nowrap;
    }
    table.responsive-table tbody tr {
      display: inline-block;
      vertical-align: top;
    }
    table.responsive-table th {
      display: block;
      text-align: right;
    }
    table.responsive-table td {
      display: block;
      min-height: 1.25em;
      text-align: left;
    }
    table.responsive-table tr {
      padding: 0 10px;
    }
    table.responsive-table thead {
      border: 0;
      border-right: 1px solid #d0d0d0;
    }
    table.responsive-table.bordered th {
      border-bottom: 0;
      border-left: 0;
    }
    table.responsive-table.bordered td {
      border-left: 0;
      border-right: 0;
      border-bottom: 0;
    }
    table.responsive-table.bordered tr {
      border: 0;
    }
    table.responsive-table.bordered tbody tr {
      border-right: 1px solid #d0d0d0;
    }
  }
</style>
