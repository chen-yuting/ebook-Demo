<template>
  <div class="ebook">
    <!-- 标题栏 -->
    <TitleBar :ifTitleAndMenuShow="ifTitleAndMenuShow"></TitleBar>
    <div class="read-wrapper">
      <div id="read"></div>
      <!-- 左右侧翻页遮罩 -->
      <div class="mask">
        <div class="left" @click="prevPage()"></div>
        <div class="center" @click="toggleTitleMenu()"></div>
        <div class="right" @click="nextPage()"></div>
      </div>
    </div>
    <!-- 菜单栏 -->
    <MenuBar :ifTitleAndMenuShow="ifTitleAndMenuShow" :fontSizeList="fontSizeList" ref="menuBar"></MenuBar>
  </div>
</template>

<script>
import TitleBar from "@/components/TitleBar";
import MenuBar from "@/components/MenuBar";
import Epub from "epubjs";
const DOWNLOAD_URL = "/static/2018_Book_AgileProcessesInSoftwareEngine.epub";
export default {
  data() {
    return {
      book: "",
      rendition: "",
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 },
      ],
    };
  },
  components: {
    TitleBar,
    MenuBar,
  },
  mounted() {
    this.showEpub();
  },
  methods: {
    //电子书的解析和渲染
    showEpub() {
      //生成Book
      this.book = new Epub(DOWNLOAD_URL);
      console.log(this.book);
      //生成Rendition,'read'是DOM
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight,
      });
      //通过Rendition.display渲染电子书
      this.rendition.display();
    },
    prevPage() {
      if (this.rendition) {
        this.rendition.prev();
      }
    },
    nextPage() {
      if (this.rendition) {
        this.rendition.next();
      }
    },
    //切换标题与菜单栏显示状态
    toggleTitleMenu() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
      if (!this.ifTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import "assets/styles/global";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      display: flex;
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>