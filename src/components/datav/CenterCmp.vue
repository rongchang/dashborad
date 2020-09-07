<template>
  <div class="center-cmp">
    <div class="cc-header">
      <dv-decoration-1 style="width:200px;height:50px;" />
      <div>综合目标</div>
      <dv-decoration-1 style="width:200px;height:50px;" />
    </div>

    <div class="cc-details">

<!--      <div>目标</div>-->
      <dv-digital-flop :config="config1" style="width:600px;height:300px;" />
<!--      <div class="card">5</div>-->
<!--      <div class="card">0</div>-->
<!--      <div class="card">0</div>-->
<!--      <div class="card">0</div>-->
<!--      <div class="card">w</div>-->
    </div>

    <div class="cc-main-container">
      <div class="ccmc-left">
        <div class="station-info" >
          <p style="position: absolute; left: 30px;top: -50px;color: rgb(0, 186, 255);">剩余</p>
          <span>{{surplus_num}}w</span>
        </div>
        <div class="station-info">
          <p style="position: absolute; left: 30px;top: -15px;color: rgb(61, 231, 201);">拼团</p><span style="position: absolute;top: 50px;">{{shopping_price}}</span>
        </div>
      </div>

      <dv-active-ring-chart class="ccmc-middle" :config="config" />

      <div class="ccmc-right">
        <div class="station-info">
          <span>{{paylog_price}}</span><p style="position: absolute;right: 100px;top: -50px;">充值</p>
        </div>
        <div class="station-info">
          <span style="position: absolute;top: 50px;">{{order_num}}w</span><p style="position: absolute;right: 100px;top: -15px;color: rgb(255, 197, 48);">订单</p>
        </div>
      </div>

      <LabelTag :config="labelConfig" />
    </div>
  </div>
</template>

<script>
import LabelTag from './LabelTag'
import mqtt from 'mqtt'
let mqclient
mqclient = mqtt.connect('ws://111.231.29.18:15675/ws', {
  username: 'yaoyao',
  password: 'Xiaojingling%!8'
})

export default {
  name: 'CenterCmp',
  components: {
    LabelTag
  },
  data () {
    return {
      config1:{},
      config: {},
      surplus_price:0,
      shopping_price:0,
      paylog_price:0,
      order_price:0,
      surplus_num:0,
      order_num:0,
      number:0,
      labelConfig: {
        data: ['剩余', '拼团', '充值', '订单']
      }
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
        var msg = JSON.parse(message)
        this.surplus_price = msg.total.surplus_price;
        this.shopping_price = msg.total.shopping_price;
        this.paylog_price = msg.total.paylog_price;
        this.order_price = msg.total.order_price;
        this.order_num = Number(msg.total.order_price / 10000).toFixed(0);
        this.surplus_num = Number(msg.total.surplus_price / 10000).toFixed(0);
        this.number = 4999;
        this.createData();
      })
    },

    createData () {
      this.config = {
        data: [
          {
            name: '剩余',
            value: this.surplus_price
          },
          {
            name: '拼团',
            value: this.shopping_price
          },
          {
            name: '充值',
            value: this.paylog_price
          },
          {
            name: '订单',
            value: this.order_price
          }
        ],
        color: ['#00baff', '#3de7c9', '#fff', '#ffc530', '#469f4b'],
        lineWidth: 30,
        radius: '55%',
        activeRadius: '60%'
      },
      this.config1 = {
        number: [this.number],
        toFixed: 0,
        content: '{nt}w',
        style: {
          fontSize: 100,
          fill: '#3de7c9'
        }
      }
    }
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response => {
      var msg = response.body.data;
      console.log(111111)
      console.log(msg)
      this.surplus_price = msg.total.surplus_price;
      this.shopping_price = msg.total.shopping_price;
      this.paylog_price = msg.total.paylog_price;
      this.order_price = msg.total.order_price;
      this.order_num = Number(msg.total.order_price / 10000).toFixed(0);
      this.surplus_num = Number(msg.total.surplus_price / 10000).toFixed(0);
      this.number=5000;
      this.createData();
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  .center-cmp {
    width: 100%;
    height: 100%;
    margin: 0px;
    padding: 0px;
    display: flex;
    flex-direction: column;
    .cc-header {
      height: 70px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 30px;
    }
    .cc-details {
      height: 120px;
      display: flex;
      justify-content: center;
      font-size: 32px;
      align-items: center;
      .card {
        background-color: rgba(4,49,128,.6);
        color: #08e5ff;
        height: 70px;
        width: 70px;
        font-size: 45px;
        font-weight: bold;
        line-height: 70px;
        text-align: center;
        margin: 2px;
      }
    }
    .cc-main-container {
      position: relative;
      flex: 1;
      display: flex;
      .ccmc-middle {
        width: 50%;
        height: 90%;
        .active-ring-name {
          font-size: 20px !important;
        }
      }
      .ccmc-left, .ccmc-right {
        width: 25%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        font-size: 24px;
        span {
          font-size: 40px;
          font-weight: bold;
        }
        .station-info {
          height: 80px;
          display: flex;
          align-items: center;
          width: 150px;
          padding-left: 10px;
          position: relative;
        }
      }
      .ccmc-left {
        align-items: flex-end;
        span {
          margin-left: 20px;
        }
      }
      .ccmc-right {
        align-items: flex-start;
        span {
          margin-right: 20px;
        }
      }
    }
    .label-tag {
      position: absolute;
      width: 500px;
      height: 30px;
      bottom: 10px;
      left: 50%;
      transform: translateX(-50%);
    }
  }
</style>
