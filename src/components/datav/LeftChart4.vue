<template>
  <div class="left-chart-4">
    <div class="lc4-title">收入类型</div>
    <div class="lc4-details">总收入金额<span>{{total}}</span></div>
    <dv-charts class="lc4-chart" :option="option" />
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
  name: 'LeftChart4',
  data () {
    return {
      option: {},
      e_price:0,
      express_price:0,
      shopping_price:0,
      paylog_price:0,
      suxi_price:0,
      total:0
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
        var data = JSON.parse(message)
        var msg = data.total
        this.e_price = msg.e_price
        this.express_price = msg.express_price
        this.shopping_price = msg.shopping_price
        this.paylog_price = msg.paylog_price
        this.suxi_price = msg.suxi_price
        this.total = Number(this.e_price) + Number(this.express_price) + Number(this.shopping_price) + Number(this.paylog_price) + Number(this.suxi_price)
        this.createData()
      })
    },
    createData () {
      this.option = {
        series: [
          {
            type: 'pie',
            data: [
              { name: '小E订单', value: this.e_price },
              { name: '快递订单', value: this.express_price },
              { name: '5小时快洗', value: this.suxi_price },
              { name: '拼团', value: this.shopping_price },
              { name: '充值', value: this.paylog_price }
            ],
            radius: ['45%', '65%'],
            insideLabel: {
              show: false
            },
            outsideLabel: {
              labelLineEndLength: 10,
              formatter: '{percent}%\n{name}',
              style: {
                fontSize: 14,
                fill: '#fff'
              }
            }
          }
        ],
        color: ['#00baff', '#3de7c9', '#fff', '#ffc530', '#469f4b']
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response => {
      var msg = response.body.data.total;
      console.log(msg)
      this.e_price = msg.e_price
      this.express_price = msg.express_price
      this.shopping_price=msg.shopping_price
      this.paylog_price=msg.paylog_price
      this.suxi_price=msg.suxi_price
      this.total=Number(this.e_price) + Number(this.express_price) + Number(this.shopping_price) + Number(this.paylog_price) + Number(this.suxi_price)
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  .left-chart-4 {
    width: 100%;
    height: 33%;
    display: flex;
    flex-direction: column;
    background-color: rgba(6, 30, 93, 0.5);
    padding-bottom: 20px;

    .lc4-title {
      text-align: center;
      height: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
      /*margin-bottom: 20px;*/
      font-weight: bold;
      text-indent: 20px;
      font-size: 20px;
    }
    .lc4-header {
      height: 20px;
      line-height: 20px;
      font-size: 16px;
      text-indent: 20px;
      margin-top: 10px;
    }

    .lc4-details {
      height: 40px;
      font-size: 16px;
      display: flex;
      align-items: center;
      text-indent: 20px;
      span {
        color: #f46827;
        font-weight: bold;
        font-size: 25px;
        margin-left: 20px;
      }
    }

    .lc4-chart {
      height: calc(~"100% - 70px");
    }
  }
</style>
