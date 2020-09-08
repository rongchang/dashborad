<template>
  <div id="rose-chart" style="margin-left: 7px;">
    <div class="rose-chart-title">城市订单金额分布</div>
    <dv-charts :option="option" />
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
  name: 'RoseChart',
  data () {
    return {
      option: {},
      city_data:[
        { name: '深圳', value: 34},
        { name: '北京', value: 440},
        { name: '杭州', value: 6},
        { name: '上海', value: 78}
      ]
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
        console.log('收到来自', topic, '的消息', message)
        var data=JSON.parse(message);
        var msg=data.city;
        var arr=[];
        for (var i=0;i<msg.length;i++) {
          arr.push({'name':msg[i].name,"value":Number(msg[i].num)})
        }
        this.city_data=arr;
        // this.order_timeout=msg.timeout_count.order_timeout;
        // this.qujian_timeout_count=msg.timeout_count.qujian_timeout_count;
        // this.quwuliu_timeout_count=msg.timeout_count.quwuliu_timeout_count;
        // this.qingxi_timeout_count=msg.timeout_count.qingxi_timeout_count;
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
            data: this.city_data,
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
        color: ['#00baff', '#3de7c9', '#ffc530', '#469f4b' ]
      }
    },
  },
  mounted () {
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderData').then(response =>{
      var msg=response.body.data.city;
      var arr=[];
      for (var i=0;i<msg.length;i++) {
        arr.push({'name':msg[i].name,"value":Number(msg[i].num)})
      }
      this.city_data=arr;
      // this.city_beijing=msg.city.;
      // this.city_shanghai=msg.timeout_count.qujian_timeout_count;
      // this.city_shenzhen=msg.timeout_count.quwuliu_timeout_count;
      // this.city_nanjing=msg.timeout_count.qingxi_timeout_count;
      this.createData();

    }, response=>{
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  #rose-chart {
    width: 30%;
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
