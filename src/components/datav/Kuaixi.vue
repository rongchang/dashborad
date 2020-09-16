<template>
  <div id="kuaixi">
    <div class="rose-chart-title">快洗超时占比</div>
    <dv-charts class="kuaixi-chart" :option="option" />
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
  name: 'Kuaixi',
  data () {
    return {
      option: {},
      qu_finish:0,
      xiyidian_qianshou:0,
      song_paidan:0,
      user_qianshou:0
    }
  },
  created () {
    this.getMsg()
  },
  methods: {
    getMsg () {
      mqclient.on('connect', function () {
        mqclient.subscribe('order_status', { qos: 0 }, function (err) {
          if (!err) {
            console.log('订阅成功')
          }
        })
      })

      mqclient.on('message', (topic, message) => {
        var data = JSON.parse(message)
        console.log('gggg')
        console.log(data)
        this.qu_finish = data.qu_finish;
        this.xiyidian_qianshou = data.xiyidian_qianshou;
        this.song_paidan = data.song_paidan;
        this.user_qianshou = data.user_qianshou;
        this.createData()
      })
    },
    createData () {
      this.option = {
        series: [
          {
            type: 'pie',
            data: [
              { name: '取件超时', value: this.qu_finish },
              { name: '取件运输超时', value: this.xiyidian_qianshou },
              { name: '清洗超时', value: this.song_paidan },
              { name: '送件超时', value: this.user_qianshou }
              // { name: '其他', value: 52 }
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
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderStatus').then(response => {
      var data = response.body.data;
      console.log(1111)
      console.log(data)
      this.qu_finish = data.qu_finish;
      this.xiyidian_qianshou = data.xiyidian_qianshou;
      this.song_paidan = data.song_paidan;
      this.user_qianshou = data.user_qianshou;
      this.createData()
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
#kuaixi {
  width: 30%;
  height: 100%;
  background-color: rgba(6, 30, 93, 0.5);
  border-top: 2px solid rgba(1, 153, 209, .5);
  box-sizing: border-box;
  margin-left: 20px;
  padding-bottom: 20px;

  .rose-chart-title {
    height: 50px;
    font-weight: bold;
    text-indent: 20px;
    font-size: 20px;
    display: flex;
    align-items: center;
  }
  .kuaixi-chart{
    height: calc(~"100% - 80px");
  }
}
</style>
