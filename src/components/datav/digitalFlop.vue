<template>
  <div id="digital-flop">
    <div
      class="digital-flop-item"
      v-for="item in digitalFlopData"
      :key="item.title"
    >
      <div class="digital-flop-title">{{ item.title }}</div>
      <div class="digital-flop">
        <dv-digital-flop
          :config="item.number"
          style="width:100px;height:50px;"
        />
          <div class="unit">{{ item.unit }}</div>
      </div>
    </div>

    <dv-decoration-10 />
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
  name: 'DigitalFlop',
  data () {
    return {
      digitalFlopData: [],
      order_price: 0,
      shopping_price:0,
      paylog_price:0,
      e_price:0,
      express_price:0,
      suxi_price:0,
      new_price:0,
      keyaccount_money:0,
      alipay:0,
      ecard_price:0
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
        console.log('收到来自', topic, '的消息hhh', message.toString())
        var msg = JSON.parse(message)
        this.shopping_price = Number(msg.total.shopping_price);
        this.paylog_price = Number(msg.total.paylog_price);
        this.order_price = Number(msg.total.order_price);
        this.alipay = Number(msg.total.alipay_price);
        this.e_price = Number(msg.total.e_price);
        this.express_price = Number(msg.total.express_price);
        this.suxi_price = Number(msg.total.suxi_price);
        this.new_price = Number(msg.total.new_price);
        this.keyaccount_money = Number(msg.total.keyaccount_money);
        this.ecard_price = Number(msg.total.ecard_price);
        this.createData()
      })
    },

    createData () {
      const { randomExtend } = this

      this.digitalFlopData = [
        {
          title: '小E订单收入',
          number: {
            number: [this.e_price],
            content: '{nt}',
            formatter: 123456789,
            textAlign: 'center',
            style: {
              fill: '#F77976',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },
        {
          title: '快递订单收入',
          number: {
            number: [this.express_price],
            content: '{nt}',
            textAlign: 'center',
            style: {
              fill: '#F77976',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },

        {
          title: '充值收入',
          number: {
            number: [this.paylog_price],
            content: '{nt}',
            textAlign: 'center',
            style: {
              fill: '#f46827',
              fontWeight: 'bold',
              fontSize:25
            }

          },
          unit: '元'
        },
        {
          title: '拼团收入',
          number: {
            number: [this.shopping_price],
            content: '{nt}',
            textAlign: 'center',
            style: {
              fill: '#A0E426',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },
        {
          title: '支付宝收入',
          number: {
            number: [this.alipay],
            content: '{nt}',
            textAlign: 'center',
            style: {
              fill: '#33A8C7',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },
        {
          title: '小e购卡收入',
          number: {
            number: [this.ecard_price],
            content: '{nt}',
            textAlign: 'center',
            style: {
              fill: '#FFAB00',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },
        {
          title: '新用户收入',
          number: {
            number: [ this.new_price ],
            content: '{nt}',
            textAlign: 'right',
            style: {
              fill: '#F050AE',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        },
        {
          title: '大客户回款',
          number: {
            number: [ this.keyaccount_money ],
            content: '{nt}',
            textAlign: 'right',
            style: {
              fill: '#9336FD',
              fontWeight: 'bold',
              fontSize:25
            }
          },
          unit: '元'
        }
      ]
    },
    randomExtend (minNum, maxNum) {
      if (arguments.length === 1) {
        return parseInt(Math.random() * minNum + 1, 10)
      } else {
        return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10)
      }
    }
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response => {
      var msg = response.body.data;
      console.log(msg)
      this.shopping_price = Number(msg.total.shopping_price);
      this.paylog_price = Number(msg.total.paylog_price);
      this.order_price = Number(msg.total.order_price);
      this.alipay = Number(msg.total.alipay_price);
      this.e_price = Number(msg.total.e_price);
      this.express_price = Number(msg.total.express_price);
      this.suxi_price = Number(msg.total.suxi_price);
      this.new_price = Number(msg.total.new_price);
      this.keyaccount_money=Number(msg.total.keyaccount_money);
      this.ecard_price=Number(msg.total.ecard_price);
      this.createData();
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  #digital-flop {
    position: relative;
    height: 15%;
    flex-shrink: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: rgba(6, 30, 93, 0.5);

    .dv-decoration-10 {
      position: absolute;
      width: 96%;
      left: 2.5%;
      height: 5px;
      bottom: 0px;
    }

    .digital-flop-item {
      width: 13%;
      height: 80%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      border-left: 3px solid rgb(6, 30, 93);
      border-right: 3px solid rgb(6, 30, 93);
    }

    .digital-flop-title {
      font-size: 20px;
      font-weight: bold;
      margin-bottom: 20px;
    }

    .digital-flop {
      display: flex;
    }

    .unit {
      margin-left: 10px;
      font-size: 20px;
      display: flex;
      align-items: flex-end;
      box-sizing: border-box;
      padding-bottom: 13px;
    }
  }
</style>
