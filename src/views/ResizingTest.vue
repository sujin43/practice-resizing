<template>
  <div id="app">
    <div class="top"><span>TOP</span></div>
    <div id="leftPanel" class="left-panel">
      <div class="area area1">
        <span>left area1</span>
        <div id="resizerV" class="resize-v"></div>
      </div>
      <div class="area area2">
        <span>left area2</span>
        <br/>
        <button class="btn-" type="button" v-on:click="toggleArea()">toggle area1</button>
      </div>
      <div id="resizerH" class="resize-h"></div>
    </div>
    <div class="right-panel"></div>
    <div class="bottom"><span>BOTTOM</span></div>
    <!-- 
      좌측 패널의 우측 가장자리를 드래그하여 너비를 조정할 수 있습니다.
      너비는 최대 520, 최소 320 안에서 조정 가능합니다.
      좌측 패널의 area1, area2 사이의 바를 드래그하여 area1,2의 높이를 조정합니다.
      area1,2의 모두 최소 높이는 200입니다.
      'toggle area1' 버튼을 클릭하면 .area1 영역을 show/hide 시킵니다.
      area1이 hide 되면 area2의 높이는 감춰진 area1의 높이만큼 늘어나고,
      area1이 show 되면 area1, 2의 높이는 area1이 hide 되기 전의 각각 높이로 돌아갑니다.
    -->
  </div>
</template>


<script>
export default {
  name: 'ResizingTest',
  data() {
    return {
      topPanel: 48, //탑 패널 높이
      leftPanel: {
        height: 0,
        minWidth: 320, //레프트 패널 최소 너비
        maxWidth: 520, //레프트 패널 최대 너비
        area1: {
          minHeight: 200, //레프트 패널 area1 최소 높이
        },
        area2: {
          minHeight: 200, //레프트 패널 area2 최소 높이
        }
      },
      showPanel: true,
      elLeftPanel: null,
      prevH: 0,
      area1: null,
      area2: null
    };
  },
  mounted() {
    this.init();
    this.resizePanelWidth();
    this.resizePanelRows();
  },
  methods: {
    init() {
      this.elLeftPanel = document.getElementById('leftPanel');
      this.leftPanel.height = this.elLeftPanel.offsetHeight;
      this.area1 = document.getElementsByClassName('area1')[0];
      this.area2 = document.getElementsByClassName('area2')[0];
    },
    resizePanelWidthAction(e){
      if(e.x >= this.leftPanel.minWidth && e.x <= this.leftPanel.maxWidth) {
        this.elLeftPanel.style.width = e.x + 'px';
      }
    },
    resizePanelWidth(){ //leftPanel 너비 리사이즈
      const resizer = document.getElementById('resizerH');
      resizer.addEventListener("mousedown", ()=> { 
        document.querySelectorAll("body")[0].style.cursor = "ew-resize";
        document.addEventListener("mousemove", this.resizePanelWidthAction, false);
      }, false)
      document.addEventListener("mouseup", ()=> {
        document.querySelectorAll("body")[0].style.cursor = "";
        document.removeEventListener("mousemove", this.resizePanelWidthAction, false);
      }, false)
    },

    resizePanelHeightAction(e){
      const fullHeight = this.leftPanel.height + this.topPanel;
      if(e.y >= (200 + this.topPanel) && (fullHeight - e.y) >= 200) {
        this.area1.style.height = (e.y - this.topPanel) + 'px';
        this.area2.style.height = (fullHeight - e.y) + 'px';
      }
    },
    resizePanelRows(){ //레프트 패널 area1,2 높이 리사이즈
      const resizer = document.getElementById('resizerV')
      resizer.addEventListener("mousedown", ()=> {
        document.querySelectorAll("body")[0].style.cursor = "ns-resize";
        document.addEventListener("mousemove", this.resizePanelHeightAction, false);
      }, false)
      document.addEventListener("mouseup", ()=> {
        document.querySelectorAll("body")[0].style.cursor = "";
        document.removeEventListener("mousemove", this.resizePanelHeightAction, false);
      }, false)
    },

    toggleArea() {
      this.showPanel = !this.showPanel;
      if(this.showPanel) {
        this.area1.style.display = "block";
        this.area2.style.height = this.leftPanel.height - this.prevH + "px";
      } else {
        this.prevH = this.area1.offsetHeight;
        this.area1.style.display = "none"
        this.area2.style.height = "100%";
      }
    }
  }
};
</script>

<style lang="scss">
  $top-height: 48px;
  $bottom-height: 32px;
  $left1-height: 300px;
  @mixin flex-row-center {
    display: flex;
    flex-direction: row;
    align-items: center;    
  }
  body,
  html {
    height: 100%;
    * {
      box-sizing: border-box;
      -webkit-user-select: none;
      -ms-user-select: none;
      user-select: none;
    }
  }
  #app {
    height: 100%;
    .top {
      @include flex-row-center;
      justify-content: center;
      position: absolute;
      left: 0;
      top: 0;
      height: $top-height;
      width: 100%;
      padding: 0 1em;
      border-bottom: 1px solid #f1f1f1;
    }
    .left-panel {
      position: absolute;
      top: $top-height;
      left: 0;
      width: 320px;
      height: calc(100% - (#{$top-height + $bottom-height}));
      background-color: #f9f9f9;
      border-right: 1px solid #f3f3f3;
      .area {
        padding: .5em;
        &.area1 {
          position: relative;
          height: $left1-height;
          border-bottom: 1px solid #f3f3f3;
          .resize-v {
            position: absolute;
            left: 0;
            bottom: -2px;
            height: 4px;
            width: 100%;
            cursor: row-resize;
          }
        }
        &.area2 {
          height: calc(100% - #{$left1-height})
        }
      }
      .resize-h {
        position: absolute;
        right: -2px;
        top: 0;
        width: 4px;
        height: 100%;
        cursor: ew-resize;
      }
    }
    .bottom {
      @include flex-row-center;
      justify-content: center;
      position: absolute;
      left: 0;
      bottom: 0;
      height: $bottom-height;
      width: 100%;
      padding: 0 1em;
      background-color: #000;
      color: #fff;
    }
    .btn- {
      padding: .3em;
      border-radius: 3px;
      background-color: #333;
      color: #fff;
    }
  }
</style>
