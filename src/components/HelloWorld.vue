<template>
  <div class="hello">
    <div :style="{
      height: `${data.mainSize.height}px`,
      width:`${data.mainSize.width}px`,
      backgroundImage:`url(${data.imageSrc})`,
      backgroundSize:`cover`}"
         id="chinaMap"
    >
    </div>
    <div class="ceshi" @click="ceshi">数据测试</div>
  </div>
</template>

<script>
// import * as echarts from 'echarts/core';
// import {
//   GeoComponent,
//   TitleComponent,
//   ToolboxComponent,
//   TooltipComponent,
//   VisualMapComponent,
// } from 'echarts/components';
// import {EffectScatterChart, MapChart} from 'echarts/charts';
// import {CanvasRenderer} from 'echarts/renderers';
import * as echarts from 'echarts'
import chinaJson from '../assets/json/china.json'
import {onMounted, onUnmounted, reactive} from "vue";

// echarts.use([
//   TitleComponent,
//   ToolboxComponent,
//   TooltipComponent,
//   VisualMapComponent,
//   GeoComponent,
//   MapChart,
//   CanvasRenderer,
//   EffectScatterChart
// ]);

export default {
  name: 'HelloWorld',
  setup() {
    const data = reactive({
      cityData: [],
      myChart: '',
      imageSrc: require('../assets/1111.jpg'),
      mainSize: {
        width: window.innerWidth,
        height: window.innerHeight
      }
    })
    const getWindowInfo = () => {
      const windowInfo = {
        width: window.innerWidth,
        height: window.innerHeight
      }
      data.mainSize = windowInfo
      myChart.resize()
      console.log(windowInfo);
    }
    window.addEventListener('resize', getWindowInfo)


    onUnmounted(() => {
    })
    onMounted(() => {
      drawChina()
      console.log('1111')
    })


    for (let i = 0; i < chinaJson.features.length; i++) {
      let c = chinaJson.features[i].properties.name
      data.cityData.push({name: c, value: Math.round(Math.random() * 100000) / 100})
    }

    let option = {
      tooltip: {},
      colorBy: 'series',
      color: ['#3682be', '#45a776', '#f05326', '#eed777', '#334f65', '#b3974e',
        '#38cb7d', '#ddae33', '#844bb3', '#93c555', '#5f6694', '#df3881'],
      dataset: {
        source: data.cityData
      },
      //配置属性
      visualMap: {
        min: 0,
        max: 1000,
        text: ['高', '低'],
        realtime: false,
        calculable: false,
        itemHeight: 300,
        itemWidth: 40,
        borderWidth: 2,
        borderColor: 'rgba(255,255,255,0.34)',
        textStyle: {
          color: '#fff',
          fontSize: 24,
          fontWeight: 'bold'
        },
        inRange: {
          color: ['#10dbff', '#7dff3c', '#ff1616'],
        }
      },
      xAxis: {
        type: 'value'
      },
      yAxis: {
        // data: data.xAxisData,
        type: 'category',
        inverse: true,

      },
      grid: {
        show: true,
        right: 120,
        top: 100,
        width: 500,
      },
      // 组件配置
      series: [
        //地图
        {
          colorBy: 'series',
          type: 'map',
          map: 'china',
          name: '成交商户数量',
          coordinateSystem: 'geo',
          left: 120,
          top: 100,
          roam: true,

          scaleLimit: {
            max: 5,
            min: 1
          },
          showEffectOn: 'render',
          itemStyle: {
            areaColor: 'rgb(5,39,175)',
            color: '#fff',
            borderColor: 'rgb(158,236,246)',
            borderWidth: 2,
          },
          emphasis: {
            focus: 'self',
            label: {
              color: '#fff',
              fontSize: 24,
            },
            itemStyle: {
              areaColor: 'rgb(2,25,117)',
              color: '#fff',
              shadowColor: 'rgb(87,120,250)',
              shadowBlur: 10,
              shadowOffsetX: 10,
              shadowOffsetY: 10
            },
          },
          select: {
            label: {
              color: '#fff',
              fontSize: 24,
            },
            itemStyle: {
              areaColor: 'rgb(2,25,117)',
              color: '#fff',
              shadowColor: 'rgb(87,120,250)',
              shadowBlur: 10,
              shadowOffsetX: 10,
              shadowOffsetY: 10
            },
          },
          tooltip: {
            formatter: function (p) {
              console.log(p)
              return `<div style="display: flex"><div style="margin-right:10px;height: 20px;width: 20px;border-radius: 50%;background: ${p.color}"></div><div>签约商户数量</div></div><div>${p.name}-${p.value}</div>`
            }
          },
          zlevel: 1,
        },

        // 柱状图
        {
          type: 'bar',
          name: '成交数量对比',
          color: 'series',
          realtimeSort: true,
          itemStyle: {
            color: 'auto'
          },
          emphasis: {
            focus: 'self',
            blurScope: 'coordinateSystem',
            label: {
              color: '#fff',
              fontSize: 24,
              offset: [0, 30]
            },

          },
          label: {
            show: true,

          },
        }
      ],
    }
    let myChart

    function drawChina() {
      myChart = echarts.init(document.getElementById('chinaMap'))
      echarts.registerMap('china', chinaJson) //注册可用的地图
      myChart.setOption(option)
    }

    function ceshi() {
      console.log(data.cityData)
    }

    return {
      data,
      ceshi
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.ceshi {
  height: 50px;
  width: 100px;
  border-radius: 10px;
  background: white;
  color: black;
  font-weight: bold;
  line-height: 50px;
  text-align: center;
  position: absolute;
  top: 30px;
  left: 20px;
  cursor: pointer;
}

</style>
