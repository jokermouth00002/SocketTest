<template lang='pug'>
  .home
    .concent(ref='concent')
</template>

<script>
// @ is an alias to /src
import * as echarts from 'echarts'
export default {
  name: 'Home',
  components: {},
  created() {
    window.home = this
    this.socket()
  },
  mounted() {},
  data() {
    return {
      rawData: []
    }
  },
  methods: {
    socket() {
      const ws = new WebSocket('ws://107.152.42.244:9502')
      ws.onopen = f => {
        console.log('open connection', f)
      }
      ws.onclose = () => {
        console.log('close socket')
      }
      ws.onmessage = m => {
        const data = JSON.parse(m.data)
        if (this.rawData.length < 61) this.rawData.push(Object.values(data))
        else {
          this.rawData.shift()
        }
        this.drawChart()
      }
      ws.onerror = err => {
        console.error(err)
      }
      window.ws = ws
    },
    drawChart() {
      const myChart = echarts.init(this.$refs.concent)

      const dates = this.rawData.map(item => {
        return item[0]
      })
      const data = this.rawData.map(item => {
        return [+item[1], +item[2], +item[3], +item[4]]
      })
      this.data1 = data
      const option = {
        backgroundColor: '#21202D',
        legend: {
          data: ['幣蜂Socket測試'],
          inactiveColor: '#777',
          textStyle: {
            color: '#fff'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            animation: false,
            type: 'cross',
            lineStyle: {
              color: '#376df4',
              width: 2,
              opacity: 1
            }
          }
        },
        xAxis: {
          type: 'category',
          data: dates,
          axisLine: { lineStyle: { color: '#8392A5' } }
        },
        yAxis: {
          scale: true,
          axisLine: { lineStyle: { color: '#8392A5' } },
          splitLine: { show: false }
        },
        grid: {
          bottom: 80
        },
        dataZoom: [
          {
            textStyle: {
              color: '#8392A5'
            },
            handleIcon:
              'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
            handleSize: '80%',
            dataBackground: {
              areaStyle: {
                color: '#8392A5'
              },
              lineStyle: {
                opacity: 0.8,
                color: '#8392A5'
              }
            },
            handleStyle: {
              color: '#fff',
              shadowBlur: 3,
              shadowColor: 'rgba(0, 0, 0, 0.6)',
              shadowOffsetX: 2,
              shadowOffsetY: 2
            }
          },
          {
            type: 'inside'
          }
        ],
        animation: true,
        series: [
          {
            type: 'candlestick',
            name: '幣蜂Socket測試',
            data: data,
            itemStyle: {
              color: '#FD1050',
              color0: '#0CF49B',
              borderColor: '#FD1050',
              borderColor0: '#0CF49B'
            }
          },
          {
            name: 'MA5',
            type: 'line',
            data: this.calculateMA(5, data),
            smooth: true,
            showSymbol: false,
            lineStyle: {
              width: 1
            }
          },
          {
            name: 'MA10',
            type: 'line',
            data: this.calculateMA(10, data),
            smooth: true,
            showSymbol: false,
            lineStyle: {
              width: 1
            }
          },
          {
            name: 'MA20',
            type: 'line',
            data: this.calculateMA(20, data),
            smooth: true,
            showSymbol: false,
            lineStyle: {
              width: 1
            }
          },
          {
            name: 'MA30',
            type: 'line',
            data: this.calculateMA(30, data),
            smooth: true,
            showSymbol: false,
            lineStyle: {
              width: 1
            }
          }
        ]
      }
      myChart.setOption(option)
    },
    calculateMA(dayCount, data) {
      const result = []
      for (let i = 0, len = data.length; i < len; i++) {
        if (i < dayCount) {
          result.push('-')
          continue
        }
        let sum = 0
        for (let j = 0; j < dayCount; j++) {
          sum += data[i - j][1]
        }
        result.push(+(sum / dayCount).toFixed(3))
      }
      return result
    }
  }
}
</script>
<style scoped lang='scss'>
.concent {
  height: 100vh;
  width: 100%;
  padding-top: 20px;
}
</style>
