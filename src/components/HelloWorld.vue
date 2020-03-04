<template>
  <div class="hello" ref="mapbox">
  </div>
</template>

<script>
import echarts from 'echarts'//引入echarts
import 'echarts/map/js/china'//引入echarts中的中国地图
import jsonp from 'jsonp'//引入jsonp

const options = {
  title: {
    text: '累计确诊',//主标题
    subtext: '累计确诊病例数，包含治愈、死亡'//副标题
  },
  series: [{
    type: 'map',//地图
    // name: '确诊人数',//用于tooltip的显示{a}
    map: 'china',//中国地图
    label: {
      show: true,//显示对应地区名字
      color: '#660208',//地区名字颜色
      fontSize: 10//地区名字大小
    },
    itemStyle: {
      //areaColor: '#10aeb5',//对应地区颜色
      borderColor: 'rgba(50, 50, 50, 0.7)'//地区边框颜色
    },
    emphasis: {
      label: {
        color: '#660208',//高亮状态下字体颜色
        fontSize: 12//高亮状态下字体大小
      },
      itemStyle: {
        areaColor: '#c7fffd'//高亮状态下对应地区的颜色
      }
    },
    data: []//显示后台数据
  }],
  visualMap: [{
    type: 'piecewise',//分段型视觉映射组件
    pieces: [//定义每一段的范围
      {min:10000},
      {min:1000,max:9999},
      {min:100,max:999},
      {min:10,max:99},
      {min:1,max:9}
    ],
    inRange: {//定义每一段的颜色，从下到上
      color: ['#ffaa85','#ff7b69','#cc2929','#8c0d0d','#660208']
    }
  }],
  tooltip: {//高亮数据显示
    trigger: 'item',
    formatter: '地区：{b}<br />确诊：{c}'//高亮数据显示格式
  }
}

export default {
  name: 'HelloWorld',
  mounted() {
    this.getData()
    this.myChart = echarts.init(this.$refs.mapbox)
  },
  methods: {
    getData() {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',{},(err,data) => {
        if(!err) {
          //请求数据成功
          let list = data.data.list.map(item => ({
            name: item.name,
            value: item.value
          }))
          options.series[0].data = list
          this.myChart.setOption(options);
        }
      })
    }
  }
}

</script>

<style>
.hello {
  width: 734px;
  height: 562px;
}
</style>
