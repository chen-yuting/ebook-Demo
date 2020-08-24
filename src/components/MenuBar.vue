<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div
        class="menu-wrapper"
        :class="{'hide-box-shadow':ifSettingShow || !ifTitleAndMenuShow}"
        v-show="ifTitleAndMenuShow"
      >
        <div class="icon-wrapper">
          <span class="icon iconfont icon-menu iconf" @click="showSetting(3)"></span>
          <span class="icon iconfont icon-progress iconf" @click="showSetting(2)"></span>
          <span class="icon iconfont icon-bright iconf" @click="showSetting(1)"></span>
          <span class="icon iconfont icon-a iconf" @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="ifSettingShow">
        <!-- 设置字体 -->
        <div class="setting-font-size" v-if="showTag==0">
          <div class="preview-min" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
          <div class="select">
            <div
              class="select-wrapper"
              v-for="(item,index) in fontSizeList"
              :key="index"
              @click="setFontSize(item.fontSize)"
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize==item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div
            class="preview-max"
            :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}"
          >A</div>
        </div>
        <!-- 设置主题 -->
        <div class="setting-theme" v-else-if="showTag==1">
          <div
            class="setting-theme-item"
            v-for="(item,index) in themeList"
            :key="index"
            @click="setTheme(index)"
          >
            <div
              class="preview"
              :style="{background:item.style.body.background}"
              :class="{'no-border':item.style.body.background!='#fff'}"
            ></div>
            <div class="text" :class="{'selected':index==defaultTheme}">{{item.name}}</div>
          </div>
        </div>
        <!-- 设置进度条 -->
        <div class="setting-progress" v-else-if="showTag==2">
          <div class="progress-wrapper">
            <input
              class="progress"
              type="range"
              max="100"
              min="0"
              step="1"
              @change="onProgressChange($event.target.value)"
              @input="onProgressInput($event.target.value)"
              :value="progress"
              :disabled="!bookAvailable"
              ref="progress"
            />
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable?progress+'%':'加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <ContentView
      :ifShowContent="ifShowContent"
      v-show="ifShowContent"
      :navigation="navigation"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
    ></ContentView>
    <!-- 目录弹出时右侧的遮罩 -->
    <transition name="fade">
      <div class="content-mask" v-show="ifShowContent" @click="hideContent()"></div>
    </transition>
  </div>
</template>

<script>
import ContentView from "@/components/Content";
export default {
  components: {
    ContentView,
  },
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false,
    },
    fontSizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: {
      type: Boolean,
      default: false,
    },
    navigation: Object,
  },
  data() {
    return {
      ifSettingShow: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false,
    };
  },
  methods: {
    showSetting(tag) {
      this.showTag = tag;
      if (this.showTag == 3) {
        this.ifSettingShow = false;
        this.ifShowContent = true;
      } else {
        this.ifSettingShow = true;
      }
    },
    hideSetting() {
      this.ifSettingShow = false;
    },
    hideContent() {
      this.ifShowContent = false;
    },
    //设置文本字体，需回调父组件
    setFontSize(fontSize) {
      this.$emit("setFontSize", fontSize);
    },
    //设置主题，需回调父组件
    setTheme(index) {
      this.$emit("setTheme", index);
    },
    //拖动进度条时触发事件
    onProgressInput(progress) {
      this.progress = progress;
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`;
    },
    //进度条松开后触发事件，根据进度条数值跳转到指定位置
    onProgressChange(progress) {
      this.$emit("onProgressChange", progress);
    },
    jumpTo(target) {
      this.$emit("jumpTo", target);
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../assets/styles/global";

.menu-bar {
  .menu-wrapper {
    display: flex;
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 101;
    width: 100%;
    height: px2rem(48);
    background-color: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      @include center;
      .icon-menu {
        position: absolute;
        left: px2rem(20);
      }
      .icon-progress {
        position: absolute;
        left: 33%;
      }
      .icon-bright {
        position: absolute;
        left: 66%;
      }
      .icon-a {
        position: absolute;
        right: px2rem(20);
        font-size: px2rem(18);
      }
    }
  }

  .setting-wrapper {
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    width: 100%;
    height: px2rem(60);
    z-index: 101;
    background-color: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .setting-font-size {
      display: flex;
      position: relative;
      top: px2rem(20);
      .preview-min {
        position: relative;
        left: 4%;
        flex: 0 0 px2rem(40);
        @include center;
      }
      .preview-max {
        position: relative;
        right: 4%;
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select {
        display: flex;
        flex: 1;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child {
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .point-wrapper {
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #ccc;
            .point {
              @include center;
              position: absolute;
              top: px2rem(-8);
              left: px2rem(-10);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              background: white;
              border: px2rem(1) solid #ccc;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, 0.15);
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                border-radius: 50%;
                background: black;
              }
            }
          }
        }
      }
    }
    .setting-theme {
      height: 100%;
      display: flex;
      .setting-theme-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview {
          flex: 1;
          border: px2rem(1) solid #ccc;
          box-sizing: border-box;
          &.no-border {
            border: none;
          }
        }
        .text {
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #ccc;
          @include center;
          &.selected {
            color: #333;
          }
        }
      }
    }
    .setting-progress {
      position: relative;
      width: 100%;
      height: 100%;
      .progress-wrapper {
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress {
          width: 100%;
          //覆盖默认样式
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          //小圆点
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: px2rem(20);
            width: px2rem(20);
            border-radius: 50%;
            background: white;
            box-shadow: 0 4px 4px rgba(0, 0, 0, 0.15);
            border: px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper {
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        color: #333;
        font-size: px2rem(12);
        text-align: center;
      }
    }
  }

  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, 0.8);
  }
}
</style>