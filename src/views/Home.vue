<template>
<div class="container">
    <p>table表格</p>
    <my-table
      :tabledata="tableData"
      :limitdata="limitData"
      :theaddata="theadData"
      :querydata="queryData"
      :ranges="rangesData"/>
  </div>
</template>

<script>

import myTable from '@/components/myTable.vue'
export default {
  name: 'Home',
  components: {
    myTable
  },
  data () {
    return {
      // 分页数据
      limitData: [3, 5, 10, 15],
      // 表格头部（是否排序）
      theadData: [
        {
          name: '排名',
          isSort: true,
          sortName: 'ranking'
        },
        {
          name: '分数',
          isSort: true,
          sortName: 'rating'
        },
        {
          name: '姓名',
          isSort: false,
          sortName: 'name'
        },
        {
          name: '本周成绩',
          isSort: true,
          sortName: 'weeklyRate'
        },
        {
          name: '图片',
          isSort: false,
          sortName: 'image'
        }
      ],
      // 搜索
      queryData: [
        {
          text: '姓名',
          parameter: 'name'
        },
        {
          text: '分数',
          parameter: 'rating'
        },
        {
          text: '本周成绩',
          parameter: 'weeklyRate'
        }
      ],
      // 范围搜索
      rangesData: [
        {
          name: '分数范围',
          type: 'rating', // 筛选类型/字段
          low: 0, // 最小值
          high: 100 // 最大值
        },
        {
          name: '本周成绩',
          type: 'weeklyRate', // 筛选类型/字段
          low: 0, // 最小值
          high: 100 // 最大值
        }
      ],
      // 表格body数据
      tableData: [
        {
          id: 1,
          ranking: 1, // 排名
          rating: 97, // 分数
          name: '张三', // 姓名
          weeklyRate: 100, // 本周成绩
          image: 'http://www.pic-zhijia-qiniu.scrchz.com/FuolG3IeWRMq6UQihkCncYm6AtxK'
        },
        {
          id: 2,
          ranking: 2,
          rating: 90,
          name: '王二麻子',
          weeklyRate: 96,
          image: 'http://www.pic-zhijia-qiniu.scrchz.com/FruIt3heCRXl5Bhv44T2DetaVq9U'
        },
        {
          id: 3,
          ranking: 8,
          rating: 60,
          name: '王五',
          weeklyRate: 88,
          image: 'http://www.pic-zhijia-qiniu.scrchz.com/FuolG3IeWRMq6UQihkCncYm6AtxK'
        },
        {
          id: 4,
          ranking: 5,
          rating: 80,
          name: '李四',
          weeklyRate: 80,
          image: ''
        },
        {
          id: 5,
          ranking: 3,
          rating: 86,
          name: '赵钱',
          weeklyRate: 80,
          image: ''
        },
        {
          id: 6,
          ranking: 4,
          rating: 85,
          name: '张三',
          weeklyRate: 70,
          image: ''
        },
        {
          id: 7,
          ranking: 6,
          rating: 70,
          name: '李白',
          weeklyRate: 81,
          image: ''
        },
        {
          id: 8,
          ranking: 9,
          rating: 55,
          name: '李四',
          weeklyRate: 81,
          image: ''
        },
        {
          id: 9,
          ranking: 4,
          rating: 85,
          name: '张三',
          weeklyRate: 68,
          image: ''
        }
      ]
    }
  }
}
</script>
<style>
  /* body {
    font-family: "Helvetica Neue","Helvetica","Arial","Microsoft Yahei";
  } */
  .clear-fix:after {
    content: ".";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }
  .clear-fix {
    *+height:1%;
  }
</style>
