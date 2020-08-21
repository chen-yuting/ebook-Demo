<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div
        class="menu-wrapper"
        :class="{'hide-box-shadow':ifSettingShow || !ifTitleAndMenuShow}"
        v-show="ifTitleAndMenuShow"
      >
        <div class="icon-wrapper">
          <span class="icon iconfont icon-menu iconf"></span>
          <span class="icon iconfont icon-progress iconf"></span>
          <span class="icon iconfont icon-bright iconf"></span>
          <span class="icon iconfont icon-a iconf" @click="showSetting()">A</span>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="ifSettingShow">
        <div class="setting-font-size">
          <div class="preview" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
          <div class="select-wrapper">
            <div class="line"></div>
            <div class="point-wrapper">
              <div class="point"></div>
            </div>
          </div>
          <div
            class="preview"
            :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}"
          >A</div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false,
    },
    fontSizeList: Array,
  },
  data() {
    return {
      ifSettingShow: false,
    };
  },
  methods: {
    showSetting() {
      this.ifSettingShow = true;
    },
    hideSetting() {
      this.ifSettingShow = false;
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
    background-color: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    .setting-font-size {
      display: flex;
      .preview {
        flex: 0 0 px2rem(40);
        @include center;
      }
      .select-wrapper {
        flex: 1;
        display: flex;
        .line {
          flex: 1;
          height: 0;
          border-top: px2rem(1) solid #ccc;
        }
        .point-wrapper {
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
        }
      }
    }
  }
}
</style>