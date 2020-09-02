<template>
  <div id="right-chart">
    <div class="right-chart-title">
      今日交易额
    </div>
    <dv-charts :option="option" />
  </div>
</template>

<script>
import mqtt from 'mqtt'
let mqclient
mqclient = mqtt.connect('ws://111.231.29.18:15672/ws', {
  username: 'yaoyao',
  password: 'Xiaojingling%!8'
})
export default {
  name: 'TopRightCmp',
  data () {
    return {
      order: [],
      option: {},
      date: ['15:55:32', '15:55:09', '15:56:05', '16:57:18'],
      express_order_price: [0, 0, 0, 0],
      e_order_price: [0, 0, 0, 0],
      suxi_order_price: [0, 0, 0, 0],
      e_last_price: 0,
      express_last_price: 0,
      suxi_last_price: 0,
      min_sum: 0,
      max_sum: 400,
      interval: 20
    }
  },
  created () {
    this.getMsg()
  },
  methods: {
    getMsg () {
      mqclient.on('connect', function () {
        mqclient.subscribe('order_data', { qos: 0 }, function (err) {
          if (!err) {
            console.log('来自order的消息')
          }
        })
      })

     mqclient.on('message', (topic, message) => {
        var order=JSON.parse(message)
        var e = order.e
        var express = order.express
        var suxi = order.suxi
        var date = []
        var express_order_price=[]
        var e_order_price=[]
        var suxi_order_price=[]
        console.log(JSON.parse(message))
        for (var i = 0; i < express.length; i++) {
          date.push(express[i].time.split(' ')[1])
          express_order_price.push(Number(express[i].order_sum_price))
        }
        console.log(express_order_price)
        for (var j = 0; j < e.length; j++) {
          e_order_price.push(Number(e[j].order_sum_price))
        }
        for (var k = 0; k < suxi.length; k++) {
          suxi_order_price.push(Number(suxi[k].order_sum_price))
        }
        var leng=Number(e_order_price.length)-Number(this.e_order_price.length)
        if (leng > 1 || leng < 0) {
          console.log(99999)
          window.location.reload()
        }
        this.express_order_price=express_order_price;
        this.e_order_price=e_order_price;
        this.suxi_order_price=suxi_order_price;
        this.max_sum = order.limit.order_price_max
        this.interval = order.limit.order_price_max / 10
        this.e_last_price = e_order_price[e_order_price.length - 1]
        this.express_last_price = express_order_price[express_order_price.length - 1]
        this.suxi_last_price = suxi_order_price[suxi_order_price.length - 1]
        this.date = date
        this.createData()
      })
    },

    createData () {
      var e_name = 'e袋洗' + '(' + this.e_last_price + ')'
      var express_name = '快递' + '(' + this.express_last_price + ')'
      var suxi_name = '速洗' + '(' + this.suxi_last_price + ')'
      this.option = {
        legend: {
          data: [ {
            name: e_name,
            color: '#00baff'
          },
          {
            name: express_name,
            color: '#ff5ca9'
          }
            // {
            //   name: suxi_name,
            //   color: '#f5d94e'
            // }
          ],
          textStyle: {
            fill: '#fff'
          }
        },
        xAxis: {
          data: this.date,
          boundaryGap: false,
          axisLine: {
            style: {
              stroke: '#999'
            }
          },
          axisLabel: {
            style: {
              fill: '#999'
            }
          },
          axisTick: {
            show: false
          }
        },
        yAxis: {
          data: 'value',
          boundaryGap: false,
          splitLine: {
            show: false
          },
          axisLine: {
            style: {
              stroke: '#999'
            }
          },
          axisLabel: {
            style: {
              fill: '#999'
            }
          },
          axisTick: {
            show: false
          },
          min: this.min_sum,
          max: this.max_sum,
          interval: this.interval
        },
        series: [
          {
            data: this.e_order_price,
            type: 'line',
            name: e_name,
            smooth: true,
            lineArea: {
              show: true,
              gradient: ['rgba(55, 162, 218, 0.6)', 'rgba(55, 162, 218, 0)']
            },
            linePoint: {
              radius: 4,
              style: {
                fill: '#00db95'
              }
            }
          },
          {
            data: this.express_order_price,
            type: 'line',
            name: express_name,
            smooth: true,
            lineArea: {
              show: true,
              gradient: ['rgba(56, 166, 218, 0.6)', 'rgba(56, 166, 218, 0)']
            },
            linePoint: {
              radius: 4,
              style: {
                fill: '#ff5ca9'
              }
            }
          }
          // {
          //   data:this.suxi_order_price,
          //   type: 'line',
          //   name: suxi_name,
          //   smooth: true,
          //   lineArea: {
          //     show: true,
          //     gradient: ['rgba(56, 166, 218, 0.6)', 'rgba(56, 166, 218, 0)']
          //   },
          //   linePoint: {
          //     radius: 4,
          //     style: {
          //       fill: '#f5d94e'
          //     }
          //   }
          // }
        ]
      }
    }
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.cn/api/data/analysis/orderData').then(response =>{
      console.log('hh1')
      console.log(response.body.data)
      var order = response.body.data
      var e = order.e
      var express = order.express
      var suxi = order.suxi
      var date = []
      var express_order_price = []
      var e_order_price = []
      var suxi_order_price=[]
      for (var i = 0; i < express.length; i++) {
        date.push(express[i].time.split(' ')[1])
        express_order_price.push(Number(express[i].order_sum_price))
      }
      for (var j = 0; j < e.length; j++) {
        e_order_price.push(Number(e[j].order_sum_price))
      }
      for (var k = 0; k < suxi.length; k++) {
        suxi_order_price.push(Number(suxi[k].order_sum_price))
      }
      this.express_order_price = express_order_price;
      this.e_order_price = e_order_price;
      this.suxi_order_price = suxi_order_price;
      this.max_sum = order.limit.order_price_max
      this.interval = order.limit.order_price_max / 10
      this.date=date
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<!--<style lang="less">-->
<!--  #scroll-board {-->
<!--    width: 50%;-->
<!--    box-sizing: border-box;-->
<!--    margin-left: 20px;-->
<!--    height: 100%;-->
<!--    overflow: hidden;-->
<!--    position: relative;-->
<!--    padding: 0 50px;-->
<!--  }-->
<!--  .chart-name {-->
<!--    position: absolute;-->
<!--    right: 70px;-->
<!--    text-align: right;-->
<!--    font-size: 20px;-->
<!--    top: 10px;-->
<!--  }-->
<!--</style>-->
<style lang="less">
  #right-chart {
    width: 50%;
    height: 100%;
    background-color: rgba(6, 30, 93, 0.5);
    border-top: 2px solid rgba(1, 153, 209, .5);
    box-sizing: border-box;
    .right-chart-title {
      height: 50px;
      font-weight: bold;
      text-indent: 20px;
      font-size: 20px;
      display: flex;
      align-items: center;
    }

    .dv-charts-container {
      height: calc(~"100% - 50px");
    }
  }
  .chart-name {
    position: absolute;

    text-align: right;
    font-size: 20px;
    bottom: 260px;
  }
  #cards {
    display: flex;
    justify-content: space-between;
    height: 45%;
    width: 50%;
    .card-item {
      background-color: rgba(6, 30, 93, 0.5);
      border-top: 2px solid rgba(1, 153, 209, .5);
      width: 19%;
      display: flex;
      flex-direction: column;
    }

    .card-header {
      display: flex;
      height: 20%;
      align-items: center;
      justify-content: space-between;

      .card-header-left {
        font-size: 18px;
        font-weight: bold;
        padding-left: 20px;
      }

      .card-header-right {
        padding-right: 20px;
        font-size: 40px;
        color: #03d3ec;
      }
    }

    .ring-charts {
      height: 55%;
    }

    .card-footer {
      height: 25%;
      display: flex;
      align-items: center;
      justify-content: space-around;
    }

    .card-footer-item {
      padding: 5px 10px 0px 10px;
      box-sizing: border-box;
      width: 40%;
      background-color: rgba(6, 30, 93, 0.7);
      border-radius: 3px;

      .footer-title {
        font-size: 15px;
        margin-bottom: 5px;
      }

      .footer-detail {
        font-size: 20px;
        color: #1294fb;
        display: flex;
        font-size: 18px;
        align-items: center;

        .dv-digital-flop {
          margin-right: 5px;
        }
      }
    }
  }
</style>
