<template>
  <div id="data-view">
    <dv-full-screen-container>

      <top-header />

      <div class="main-content">

        <div class="block-left-right-content">
<!--          <ranking-board />-->

          <div class="block-top-bottom-content">
            <div class="block-top-content" style="height: 50%;">
              <ranking-board/>
              <top-left-cmp />
              <top-right-cmp />

            </div>
            <div class="block-top-content" style="height: 50%;">
<!--              <rose-left />-->
<!--              <rose-chart />-->
              <water-level-chart />
              <scroll-board/>
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
import rankingBoard from './rankingBoard'
import roseChart from './roseChart'
import roseLeft from './roseLeft'
import waterLevelChart from './waterLevelChart'
import scrollBoard from './scrollBoard'
import cards from './cards'
import topRightCmp from './topRightCmp'
import topLeftCmp from './topLeftCmp'

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
    roseLeft
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
    display: flex;
    flex-grow: 0;
    box-sizing: border-box;
  }
}
</style>
