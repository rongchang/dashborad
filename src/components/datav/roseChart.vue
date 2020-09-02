<template>
  <div id="rose-chart" style="margin-left: 7px;">
    <div class="rose-chart-title">5小时快洗超时</div>
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
  name: 'RoseChart',
  data () {
    return {
      option: {},
      order_timeout:1,
      qujian_timeout_count:1,
      quwuliu_timeout_count:1,
      qingxi_timeout_count:1,
      songwuliu_timeout_count:1
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
        this.order_timeout=msg.timeout_count.order_timeout;
        this.qujian_timeout_count=msg.timeout_count.qujian_timeout_count;
        this.quwuliu_timeout_count=msg.timeout_count.quwuliu_timeout_count;
        this.qingxi_timeout_count=msg.timeout_count.qingxi_timeout_count;
        this.songwuliu_timeout_count=msg.timeout_count.songwuliu_timeout_count;
        console.log(msg)
        console.log(this.order_timeout)
        console.log(this.qujian_timeout_count)
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
              { name: '订单超时', value: this.order_timeout},
              { name: '取件超时', value: this.qujian_timeout_count},
              { name: '取件物流超时', value: this.quwuliu_timeout_count},
              { name: '清洗超时', value: this.qingxi_timeout_count},
              { name: '送件物流超时', value: this.songwuliu_timeout_count},
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
        color: ['#da2f00', '#fa3600', '#ff4411', '#ff724c', '#541200', '#801b00', ]
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.cn/api/data/analysis/orderStatus').then(response =>{
      var msg=response.body.data;
      console.log(msg)
      this.order_timeout=msg.timeout_count.order_timeout>0?msg.timeout_count.order_timeout:1;
      this.qujian_timeout_count=msg.timeout_count.qujian_timeout_count;
      this.quwuliu_timeout_count=msg.timeout_count.quwuliu_timeout_count;
      this.qingxi_timeout_count=msg.timeout_count.qingxi_timeout_count;
      this.songwuliu_timeout_count=msg.timeout_count.songwuliu_timeout_count;
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
