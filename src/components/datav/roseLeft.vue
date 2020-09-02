<template>
  <div id="rose-chart">
    <div class="rose-chart-title">5小时快洗订单</div>
    <dv-charts :option="option" />
  </div>
</template>

<script>
  import mqtt from 'mqtt'

  let mqclient

  mqclient = mqtt.connect('ws://172.16.0.26:15675/ws', {
    username: "yt",
    password: "123456"
  })
  export default {
    name: 'RoseLeft',
    data () {
      return {
        option: {},
        dai_qu_jian_count:10,
        finish_qu_jian_count:20,
        xiyidian_qian_shou_count:30,
        fen_jian_count:40,
        song_pai_dan_count:50,
        user_qian_shou_count:60
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
          console.log('收到来自', topic, '的消息', message)
          var msg=JSON.parse(message);
          this.dai_qu_jian_count=msg.status_count.dai_qu_jian_count;
          this.finish_qu_jian_count=msg.status_count.finish_qu_jian_count;
          this.xiyidian_qian_shou_count=msg.status_count.xiyidian_qian_shou_count;
          this.fen_jian_count=msg.status_count.fen_jian_count;
          this.song_pai_dan_count=msg.status_count.song_pai_dan_count;
          this.user_qian_shou_count=msg.status_count.user_qian_shou_count;
          this.createData()
        })
      },
      createData () {
        this.option = {
          series: [
            {
              type: 'pie',
              radius: '50%',
              roseSort: false,
              data: [
                { name: '代取件', value: this.dai_qu_jian_count },
                { name: '已取件', value:  this.finish_qu_jian_count },
                { name: '洗衣店签收', value:   this.xiyidian_qian_shou_count },
                { name: '分拣', value: this.fen_jian_count },
                { name: '送派单', value: this.song_pai_dan_count },
                { name: '用户签收', value: this.user_qian_shou_count },
              ],
              insideLabel: {
                show: false
              },
              outsideLabel: {
                formatter: '{name} {value}',
                labelLineEndLength: 20,
                style: {
                  fill: '#fff'
                },
                labelLineStyle: {
                  stroke: '#fff'
                }
              },
              roseType: true
            }
          ],
          color: ['#ffff00', '#ffcc00', '#ff9900','#ff6600','#ff3300','#ff6666']
        }
      },
    },
    mounted () {
      this.$http.get('http://express.edaixipublic.cn/api/data/analysis/orderStatus').then(response =>{
        var msg=response.body.data;
        console.log(msg)
        this.dai_qu_jian_count=msg.status_count.dai_qu_jian_count;
        this.finish_qu_jian_count=msg.status_count.finish_qu_jian_count;
        this.xiyidian_qian_shou_count=msg.status_count.xiyidian_qian_shou_count;
        this.fen_jian_count=msg.status_count.fen_jian_count;
        this.song_pai_dan_count=msg.status_count.song_pai_dan_count;
        this.user_qian_shou_count=msg.status_count.user_qian_shou_count;
        this.createData();
      }, response=>{
        console.log('请求失败')
      })
    }
  }
</script>

<style lang="less">
  #rose-chart {
    width: 35%;
    height: 100%;
    background-color: rgba(6, 30, 93, 0.5);
    border-top: 2px solid rgba(1, 153, 209, .5);
    box-sizing: border-box;

    .rose-chart-title {
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
</style>
