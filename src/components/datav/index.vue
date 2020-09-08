<template>
  <div id="data-view">
    <dv-full-screen-container>
      <top-header />
      <div class="main-content">
        <digital-flop />
        <div class="block-left-right-content">
          <div class="left-chart-container">
            <Left-Chart-2 />
            <Left-Chart-3 />
            <Left-Chart-4 />
          </div>
          <div class="block-top-bottom-content">
            <div class="block-top-content">
              <rose-chart />
              <water-level-chart />
              <scroll-board />
            </div>
            <div class="block-bottom-content">
              <order />
              <kuaixi />
            </div>
          </div>
        </div>
      </div>
    </dv-full-screen-container>
  </div>
</template>

<script>
import topHeader from './topHeader'
import digitalFlop from './digitalFlop'
import roseChart from './roseChart'
import waterLevelChart from './waterLevelChart'
import scrollBoard from './scrollBoard'
import LeftChart2 from './LeftChart2'
import LeftChart3 from './LeftChart3'
import LeftChart4 from './LeftChart4'
import Kuaixi from './Kuaixi'
import Order from './Order'

export default {
  name: 'DataView',
  components: {
    topHeader,
    digitalFlop,
    roseChart,
    waterLevelChart,
    scrollBoard,
    LeftChart4,
    LeftChart2,
    LeftChart3,
    Kuaixi,
    Order
  },
  data () {
    return {}
  },
  methods: {}
}
</script>

<style lang="less">
  #data-view {
    width: 100%;
    height: 100%;
    background-color: #030409;
    color: #fff;

    #dv-full-screen-container {
      background-image: url('./img/bg.png');
      background-size: 100% 100%;
      box-shadow: 0 0 3px blue;
      display: flex;
      flex-direction: column;
    }

    .main-content {
      flex: 1;
      display: flex;
      flex-direction: column;
    }

    .block-left-right-content {
      flex: 1;
      display: flex;
      margin-top: 20px;
    }

    .block-top-bottom-content {
      flex: 1;
      display: flex;
      flex-direction: column;
      box-sizing: border-box;
      padding-left: 20px;
    }

    .block-top-content {
      height: 55%;
      display: flex;
      flex-grow: 0;
      box-sizing: border-box;
      padding-bottom: 20px;
    }
    .block-bottom-content {
      height: 60%;
      display: flex;
      flex-grow: 0;
      box-sizing: border-box;
      padding-bottom: 20px;
    }
    .left-chart-container {
      border-top: 2px solid rgba(1, 153, 209, .5);
      width: 22%;
      /*padding: 10px;*/
      box-sizing: border-box;
      .border-box-content {
        flex-direction: column;
      }
    }
  }
</style>
