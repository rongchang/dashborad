<template>
  <div class="left-chart-3">
    <div class="lc3-title">今日活动数据</div>
    <dv-capsule-chart class="lc3-chart" :config="config" />
    <dv-decoration-2 style="height:10px;" />
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
  name: 'LeftChart3',
  data () {
    return {
      config: {},
      shopping_count:0,
      shopping_sum_humans:0,
      hongbao_price:0,
      zhuanpan_price:0
    }
  },
  created () {
    this.getMsg()
  },
  methods: {
    getMsg () {
      mqclient.on('connect', function () {
        mqclient.subscribe('shopping_data', { qos: 0 }, function (err) {
          if (!err) {
            console.log('订阅成功')
          }
        })
      })

      mqclient.on('message', (topic, message) => {
        var msg = JSON.parse(message)
        console.log(msg)
        this.shopping_count = msg.shopping_count
        this.shopping_sum_humans = msg.shopping_sum_humans
        this.hongbao_price=msg.hongbao_price
        this.zhuanpan_price=msg.zhuanpan_price
        this.createData()
      })
    },
    createData () {
      this.config = {
        data: [
          {
            name: '今日拼团参与人数',
            value: this.shopping_sum_humans
          },
          {
            name: '今日成团数',
            value: this.shopping_count
          },
          {
            name: '转盘参与人数',
            value: this.zhuanpan_price

          },
          {
            name: '红包参与人数',
            value: this.hongbao_price
          }
        ],
        colors: ['#00baff', '#3de7c9', '#fff', '#ffc530', '#469f4b'],
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/shoppingData').then(response => {
      var msg = response.body.data
      console.log(msg)
      this.shopping_count = msg.shopping_count
      this.shopping_sum_humans = msg.shopping_sum_humans
      this.hongbao_price=msg.hongbao_price
      this.zhuanpan_price=msg.zhuanpan_price
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  .left-chart-3 {
    width: 100%;
    height: 34%;
    display: flex;
    flex-direction: column;
    background-color: rgba(6, 30, 93, 0.5);

    .lc3-title {
      text-align: center;
      height: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
      /*margin-bottom: 10px;*/
      font-weight: bold;
      text-indent: 20px;
      font-size: 20px;
    }
    .lc3-details {
      height: 40px;
      font-size: 16px;
      display: flex;
      align-items: center;
      text-indent: 20px;

      span {
        color: #096dd9;
        font-weight: bold;
        font-size: 35px;
        margin-left: 20px;
      }
    }

    .lc3-chart {
      flex: 1;
    }
  }
</style>
