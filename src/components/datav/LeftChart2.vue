<template>
  <div class="left-chart-2">
    <div class="lc2-title">新用户来源</div>
    <div class="lc2-details">新增用户人数<span>{{new_count}}</span></div>
    <dv-charts class="lc2-chart" :option="option" />
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
  name: 'LeftChart2',
  data () {
    return {
      option: {},
      new_count:0,
      data_count:[
        { name: '支付宝小程序', value: 2000 },
        { name: '微信公众号', value: 3000 },
        { name: 'IOS客户端', value: 4000 },
        { name: 'Android客户端', value: 1000 }
      ],
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
        var data=JSON.parse(message);
        var msg=data.user;
        var arr=[];
        for (var i=0;i<msg.length;i++) {
          arr.push({'name':msg[i].name,"value":Number(msg[i].num)})
        }
        this.new_count=data.total.user_nums
        this.data_count=arr;
        this.createData();
      })
    },
    createData () {
      this.option = {
        series: [
          {
            type: 'pie',
            data:this.data_count,
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
      var msg=response.body.data.user;
      var arr=[];
      for (var i=0;i<msg.length;i++) {
        arr.push({'name':msg[i].name,"value":Number(msg[i].num)})
      }

      console.log(arr)
      this.data_count=arr;
      this.new_count=response.body.data.total.user_nums
      this.createData();
    }, response => {
      console.log('请求失败')
    })
  }
}
</script>

<style lang="less">
  .left-chart-2 {
    width: 100%;
    height: 30%;
    display: flex;
    flex-direction: column;
    background-color: rgba(6, 30, 93, 0.5);

    .lc2-title {
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
    .lc2-details {
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

    .lc2-chart {
      height: calc(~"100% - 80px");
    }
  }
</style>
