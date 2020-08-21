<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="ifTitleAndMenuShow">
      <div class="left">
        <span class="icon iconfont icon-back iconf"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <span class="icon iconfont icon-cart iconf"></span>
          <span class="icon iconfont icon-person iconf"></span>
          <span class="icon iconfont icon-More iconf"></span>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<style lang="scss" scoped>
@import "../assets/styles/global";
.title-wrapper {
  display: flex;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 101;
  width: 100%;
  height: px2rem(48);
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
  background-color: white;
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    position: absolute;
    right: px2rem(20);
  }
}
</style>