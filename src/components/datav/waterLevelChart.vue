<template>
  <div id="water-level-chart">
    <div class="water-level-chart-title">计划收入累计完成情况</div>

    <div class="water-level-chart-details">
      累计完成<span>{{order_sum_price}}</span>元
    </div>

    <div class="chart-container" >
      <dv-water-level-pond :config="config"/>
    </div>
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
  name: 'WaterLevelChart',
  data () {
    return {
      config: {},
      order_sum_price: 0,
      percent: 0
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
            console.log('订阅成功')
          }
        })
      })

      mqclient.on('message', (topic, message) => {
        var order = JSON.parse(message)
        this.order_sum_price = order.total.order_price + order.total.paylog_price + order.total.shopping_price + order.total.keyaccount_money + order.total.ecard_price
        this.percent = ((this.order_sum_price / 21480000) * 100).toFixed(2)
        this.createData()
      })
    },
    createData () {
      this.config = {
        data: [this.percent],
        shape: 'round',
        waveHeight: 25,
        waveNum: 2
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response => {
      var order = response.body.data
      console.log(response.body.data)
      this.order_sum_price = order.total.order_price + order.total.paylog_price + order.total.shopping_price + order.total.keyaccount_money + order.total.ecard_price
      this.percent = ((this.order_sum_price / 21480000) * 100).toFixed(2)
      console.log(this.percent)
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}

</script>

<style lang="less">
  #water-level-chart {
    width: 30%;
    box-sizing: border-box;
    margin-left: 20px;
    background-color: rgba(6, 30, 93, 0.5);
    border-top: 2px solid rgba(1, 153, 209, .5);
    display: flex;
    flex-direction: column;

    .water-level-chart-title {
      font-weight: bold;
      height: 50px;
      display: flex;
      align-items: center;
      font-size: 20px;
      justify-content: center;
    }

    .water-level-chart-details {
      height: 15%;
      display: flex;
      justify-content: center;
      font-size: 17px;
      align-items: flex-end;

      span {
        font-size: 35px;
        font-weight: bold;
        color: #ff5ca9;
        margin: 0 5px;
        margin-bottom: -5px;
      }
    }

    .chart-container {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .dv-water-pond-level {
      max-width: 90%;
      width: 200px;
      height: 200px;
      border: 10px solid #19c3eb;
      border-radius: 50%;

      ellipse {
        stroke: transparent !important;
      }

      text {
        font-size: 40px;
      }
    }
  }
</style>
