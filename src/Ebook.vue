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
    <MenuBar
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onProgressChange="onProgressChange"
      :navigation="navigation"
      @jumpTo="jumpTo"
      ref="menuBar"
    ></MenuBar>
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
      themes: "",
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
      defaultFontSize: 16,
      themeList: [
        {
          name: "default",
          style: {
            body: {
              color: "#000",
              background: "#fff",
            },
          },
        },
        {
          name: "eye",
          style: {
            body: {
              color: "#000",
              background: "#ceeaba",
            },
          },
        },
        {
          name: "night",
          style: {
            body: {
              color: "#fff",
              background: "#000",
            },
          },
        },
        {
          name: "gold",
          style: {
            body: {
              color: "#000",
              background: "rgb(241,236,226)",
            },
          },
        },
      ],
      defaultTheme: 0,
      locations: "",
      //图书是否处于可用状态
      bookAvailable: false,
      navigation: {},
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
      //获取theme对象
      this.themes = this.rendition.themes;
      //设置默认字体
      this.setFontSize(this.defaultFontSize);
      //创建主题
      this.registerTheme();
      //设置默认主题
      this.setTheme(this.defaultTheme);
      //获取navigation目录
      //获取locations对象进度条，默认不会生成对象，耗性能
      //通过epubjs的钩子函数来实现
      this.book.ready
        .then(() => {
          this.navigation = this.book.navigation;
          return this.book.locations.generate();
        })
        .then((result) => {
          this.locations = this.book.locations;
          this.bookAvailable = true;
        });
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
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize;
      if (this.themes) {
        this.themes.fontSize(fontSize + "px");
      }
    },
    registerTheme() {
      this.themeList.forEach((theme) => {
        this.themes.register(theme.name, theme.style);
      });
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name);
      this.defaultTheme = index;
    },
    //progress 进度条的数值(0-100)
    onProgressChange(progress) {
      const percentage = progress / 100;
      const location =
        percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
      this.rendition.display(location);
    },
    //根据链接跳转到指定位置
    jumpTo(href) {
      this.rendition.display(href);
      this.hideTitleAndMenu();
    },
    hideTitleAndMenu() {
      //隐藏标题栏和菜单栏
      this.ifTitleAndMenuShow = false;
      //隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideSetting();
      //隐藏目录
      this.$refs.menuBar.hideContent();
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