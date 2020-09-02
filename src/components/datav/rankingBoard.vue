<template>
  <div id="ranking-board">
    <div class="ranking-board-title">拼团活动</div>
    <dv-scroll-ranking-board :config="config" />
  </div>
</template>

<script>
import mqtt from 'mqtt'
let mqclient
mqclient = mqtt.connect('ws://172.16.0.26:15675/ws', {
  username: 'yt',
  password: '123456'
})
export default {
  name: 'RankingBoard',
  data () {
    return {
      config: {},
      shopping_count: 0,
      shopping_sum_price: 0,
      shopping_sum_humans: 0
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
        this.shopping_count = msg[0].shopping_count
        this.shopping_sum_price = msg[0].shopping_sum_price
        this.shopping_sum_humans = msg[0].shopping_sum_humans
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
          }
        ],
        rowNum: 9
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.cn/api/data/analysis/shoppingData').then(response => {
      var msg = response.body.data
      console.log(msg)
      this.shopping_count = msg[0].shopping_count
      this.shopping_sum_price = msg[0].shopping_sum_price
      this.shopping_sum_humans = msg[0].shopping_sum_humans
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
#ranking-board {
  width: 20%;
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
 height:50px ;
  padding: 10px 0px;
}
</style>
