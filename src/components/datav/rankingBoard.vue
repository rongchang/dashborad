<template>
  <div id="ranking-board">
    <div class="ranking-board-title">拼团活动</div>
    <dv-scroll-ranking-board :config="config" />
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
  name: 'RankingBoard',
  data () {
    return {
      config: {},
      shopping_count: 0,
      shopping_sum_price: 0,
      shopping_sum_humans: 0,
      paylog_sum_price: 0
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
        this.shopping_sum_price = msg.shopping_sum_price
        this.shopping_sum_humans = msg.shopping_sum_humans
        this.paylog_sum_price = msg.paylog_sum_price
        console.log('收到来自', topic, '的消息', message)
        this.createData()
      })
    },
    createData () {
      this.config = {
        data: [
          {
            name: '拼团数',
            value:  this.shopping_count
          },
          {
            name: '拼团交易额',
            value: this.shopping_sum_price
          },
          {
            name: '拼团人数',
            value: this.shopping_sum_humans
          },
          {
            name: '用户充值',
            value: this.paylog_sum_price
          }
        ],
        rowNum: 9
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/shoppingData').then(response => {
      var msg = response.body.data
      console.log(msg)
      this.shopping_count = msg.shopping_count
      this.shopping_sum_price = msg.shopping_sum_price
      this.shopping_sum_humans = msg.shopping_sum_humans
      this.paylog_sum_price = msg.paylog_sum_price
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
#ranking-board {
  width: 100%;
  height: 100%;
  box-shadow: 0 0 3px blue;
  display: flex;
  flex-direction: column;
  background-color: rgba(6, 30, 93, 0.5);
  border-top: 2px solid rgba(1, 153, 209, .5);
  box-sizing: border-box;
  padding: 0px 30px;

  .ranking-board-title {
    font-weight: bold;
    height: 50px;
    display: flex;
    align-items: center;
    font-size: 20px;
  }

  .dv-scroll-ranking-board {
    flex: 1;
  }
}

.dv-scroll-ranking-board .row-item{
  height:100px ;
  /*padding: 25px 0px;*/
}
</style>
