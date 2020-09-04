
<template>
  <div id="data-view">
    <dv-full-screen-container>
      <div class="main-header">
        <div class="mh-right">

        </div>
      </div>

      <dv-border-box-1 class="main-container">
        <dv-border-box-3 class="left-chart-container">

          <ranking-board />

        </dv-border-box-3>

        <div class="right-main-container">
          <div class="rmc-top-container">
            <dv-border-box-3 class="rmctc-left-container">

              <Center-Cmp />

            </dv-border-box-3>

            <div class="rmctc-right-container">
              <dv-border-box-3 class="rmctc-chart-1">

                <scroll-board />

              </dv-border-box-3>

            </div>
          </div>

          <dv-border-box-4 class="rmc-bottom-container">

            <top-left-cmp/>
            <top-right-cmp/>

          </dv-border-box-4>
        </div>
      </dv-border-box-1>

    </dv-full-screen-container>
  </div>
</template>

<script>
import topHeader from './topHeader'
import digitalFlop from './digitalFlop'
import rankingBoard from './rankingBoard'
import roseChart from './roseChart'
import roseLeft from './roseLeft'
import waterLevelChart from './waterLevelChart'
import scrollBoard from './scrollBoard'
import cards from './cards'
import topRightCmp from './topRightCmp'
import topLeftCmp from './topLeftCmp'
import CenterCmp from './CenterCmp'

export default {
  name: 'DataView',
  components: {
    topHeader,
    digitalFlop,
    rankingBoard,
    roseChart,
    waterLevelChart,
    scrollBoard,
    cards,
    topRightCmp,
    topLeftCmp,
    roseLeft,
    CenterCmp
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
    .main-header {
      height: 80px;
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
      .mh-left {
        font-size: 20px;
        color: rgb(1,134,187);
        a:visited {
          color: rgb(1,134,187);
        }
      }
      .mh-middle {
        font-size: 30px;
      }
      .mh-left, .mh-right {
        width: 450px;
      }
    }
    .main-container {
      height: calc(~"100% - 80px");
      .border-box-content {
        padding: 20px;
        box-sizing: border-box;
        display: flex;
      }
    }
    .left-chart-container {
      width: 22%;
      padding: 10px;
      box-sizing: border-box;
      .border-box-content {
        flex-direction: column;
      }
    }
    .right-main-container {
      width: 78%;
      padding-left: 5px;
      box-sizing: border-box;
    }
    .rmc-top-container {
      height: 65%;
      display: flex;
    }
    .rmctc-left-container {
      width: 65%;
    }
    .rmctc-right-container {
      width: 35%;
    }
    .rmc-bottom-container {
      height: 35%;
    }
    .rmctc-chart-1, .rmctc-chart-2 {
      height: 100%;
    }
  }
</style>
