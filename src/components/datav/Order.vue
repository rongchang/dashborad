<template>
  <div id="order">
    <div class="order-chart-title">今日订单量</div>
    <dv-charts :option="options" />
  </div>
</template>

<script>
import mqtt from 'mqtt'

let mqclient

mqclient = mqtt.connect('ws://111.231.29.18:15675/ws', {
  username: 'yaoyao',
  password: 'Xiaojingling%!8'
})
export default {
  name: 'Order',
  data () {
    return {
      options: {},
      date: [],
      express_order_count: [],
      e_order_count: [],
      suxi_order_count: [],
      e_last_count: 0,
      express_last_count: 0,
      suxi_last_count: 0,
      min_sum: 0,
      max_sum: 0,
      interval: 1
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
            console.log('来自order的消息h1111')
          }
        })
      })

      mqclient.on('message', (topic, message) => {
        var order = JSON.parse(message)
        var e = order.e
        var express = order.express
        var suxi = order.suxi
        var date = []
        var express_order_count = []
        var e_order_count = []
        var suxi_order_count=[];
        console.log('收到来自', topic, '的消息111')
        for (var i = 0; i < express.length; i++) {
          var time = express[i].time.split(' ')[1];
          date.push(time.split(':')[0] + ':' + time.split(':')[1]);
          express_order_count.push(express[i].order_count)
        }
        for (var j = 0; j < e.length; j++) {
          e_order_count.push(e[j].order_count)
        }
        for (var k = 0; k < suxi.length; k++) {
          suxi_order_count.push(suxi[k].order_count)
        }
        console.log(Number(e_order_count.length))
        console.log(Number(this.e_order_count.length))
        var leng = Number(e_order_count.length)-Number(this.e_order_count.length)
        if (leng > 1 || leng < 0) {
          window.location.reload()
        }
        this.express_order_count=express_order_count;
        this.e_order_count=e_order_count;
        this.suxi_order_count=suxi_order_count;
        var max=0;
        this.e_last_count = e_order_count[e_order_count.length - 1]
        this.express_last_count = express_order_count[express_order_count.length - 1]
        this.suxi_last_count = suxi_order_count[suxi_order_count.length - 1]
        if(this.e_last_count>this.express_last_count){
          max=this.e_last_count;
        }else{
          max=this.express_last_count;
        }
        this.max_sum = Math.round(max+max/3)
        this.interval = Math.round(this.max_sum/5)
        this.date = date
        this.createData()
      })
    },
    createData () {
      var e_name = 'e袋洗' + '(' + this.e_last_count + ')'
      var express_name='快递' + '(' + this.express_last_count + ')'
      var suxi_name='速洗' + '(' + this.suxi_last_count + ')'
      this.options = {
        legend: {
          data: [
            {
              name: e_name,
              color: '#00baff'
            },
            {
              name: express_name,
              color: '#ff5ca9'
            }
          ],
          textStyle: {
            fill: '#fff'
          }
        },
        xAxis: {
          data: this.date,
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
            name: e_name,
            data: this.e_order_count,
            type: 'line',
            smooth: true,
            lineArea: {
              show: true,
              gradient: ['rgba(55, 162, 218, 0.6)', 'rgba(55, 162, 218, 0)']
            },
            lineStyle: {
              lineDash: [5, 5]
            },
            linePoint: {
              radius: 4,
              style: {
                fill: '#00db95'
              }
            }
          },
          {
            name: express_name,
            data: this.express_order_count,
            type: 'line',
            lineStyle: {
              stroke: '#ff5ca9'
            },
            linePoint: {
              radius: 4,
              style: {
                fill: '#ff5ca9',
                stroke: 'transparent'
              }
            }
          }

        ]
      }
    }
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response =>{
      console.log('hh22')
      console.log(response.body.data)
      var order = response.body.data;
      var e = order.e;
      var express = order.express;
      var suxi = order.suxi;
      var date = [];
      var express_order_count = [];
      var e_order_count = [];
      var suxi_order_count = [];
      for (var i = 0; i < express.length; i++) {
        var time = express[i].time.split(' ')[1];
        date.push(time.split(':')[0] + ':' + time.split(':')[1]);
        express_order_count.push(express[i].order_count)
      }
      for (var j = 0; j < e.length; j++) {
        e_order_count.push(e[j].order_count)
      }
      for (var k = 0; k < suxi.length; k++) {
        suxi_order_count.push(suxi[k].order_count)
      }
      this.express_order_count = express_order_count;
      this.e_order_count = e_order_count;
      this.suxi_order_count = suxi_order_count;
      var max=0;
      this.e_last_count = e_order_count[e_order_count.length - 1]
      this.express_last_count = express_order_count[express_order_count.length - 1]
      this.suxi_last_count = suxi_order_count[suxi_order_count.length - 1]
      if(this.e_last_count>this.express_last_count){
        max=this.e_last_count;
      }else{
        max=this.express_last_count;
      }
      this.max_sum = Math.round(max+max/3)
      this.interval = Math.round(this.max_sum/5)
      console.log(10/3)
      this.date = date
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
#order {
  width: 70%;
  height: 99%;
  background-color: rgba(6, 30, 93, 0.5);
  border-top: 2px solid rgba(1, 153, 209, .5);
  box-sizing: border-box;
  padding-bottom: 20px;

  .order-chart-title {
    height: 50px;
    font-weight: bold;
    text-indent: 20px;
    font-size: 20px;
    display: flex;
    align-items: center;
  }

}
</style>
