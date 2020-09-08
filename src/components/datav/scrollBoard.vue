<template>
  <div id="scroll-board">
    <dv-scroll-board :config="config" />
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
  name: 'ScrollBoard',
  data () {
    return {
      config: {},
      order: []
    }
  },
  created () {
    this.getMsg()
  },
  methods: {
    getMsg () {
      mqclient.on('connect', function () {
        mqclient.subscribe('complain_all_data', { qos: 0 }, function (err) {
          if (!err) {
            console.log('订阅成功')
          }
        })
      })

      mqclient.on('message', (topic, message) => {
        var msg = JSON.parse(message)
        var arr = []
        for (var i in msg) {
          var create=msg[i].created_at
          arr.push([create.split(' ')[1],msg[i].complaint])
        }
        console.log(arr)
        this.order = arr
        console.log(arr)
        this.createData()
      })
    },

    createData () {
      this.config = {
        header: ['评价日期','评价内容'],
        data: this.order,
        index: true,
        columnWidth: [80],
        align: ['center'],
        rowNum: 7,
        headerBGC: '#1981f6',
        headerHeight: 45,
        oddRowBGC: 'rgba(0, 44, 81, 0.8)',
        evenRowBGC: 'rgba(10, 29, 50, 0.8)'
      }
    }
  },
  mounted () {
    var arr = []
    this.$http.get('http://express.edaixipublic.com/api/data/analysis/orderComplain').then(response => {
      var msg = response.body.data
      console.log(55)
      console.log(response.body)
      for (var i in msg) {
        var create=msg[i].created_at
        arr.push([create.split(' ')[1],msg[i].complaint])
        this.order = arr
      }
      this.createData()
    }, response => {
      console.log('请求失败')
    })

    // setInterval(createData, 30000)
  }
}
</script>

<style lang="less">
#scroll-board {
  background-color: rgba(6, 30, 93, 0.5);
  width: 100%;
  box-sizing: border-box;
  margin-left: 7px;
  height: 100%;
  overflow: hidden;
}
</style>
