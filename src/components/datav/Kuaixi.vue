<template>
  <div id="kuaixi">
    <div class="rose-chart-title">快洗超时占比</div>
    <dv-charts class="kuaixi-chart" :option="option" />
  </div>
</template>

<script>

export default {
  name: 'Kuaixi',
  data () {
    return {
      option: {}
    }
  },
  methods: {
    createData () {
      this.option = {
        series: [
          {
            type: 'pie',
            data: [
              { name: '取件超时', value: 2000 },
              { name: '取件运输超时', value: 3000 },
              { name: '清洗超时', value: 4000 },
              { name: '送件超时', value: 1000 }
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
    const { createData } = this

    createData()

    setInterval(createData, 30000)
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
