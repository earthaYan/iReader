<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
          <div class="left" @click="prevPage"></div>
          <div class="center" @click="toggleTitleAndMenu"></div>
          <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar @setTheme="setTheme" @setFontSize="setFontSize" :themeList="themeList" :defaultTheme="defaultTheme" :defaultFontSize="defaultFontSize" :ifTitleAndMenuShow="ifTitleAndMenuShow" :fontSizeList="fontSizeList" ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar.vue'
import MenuBar from '@/components/MenuBar.vue'
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/history.epub'
global.Epub = Epub
export default{
  components: {
    TitleBar,
    MenuBar
  },
  data () {
    return {
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000',
              'background': '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              'color': '#000',
              'background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff',
              'background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#fff',
              'background': 'red'
            }
          }
        }
      ],
      defaultTheme: 0
    }
  },
  methods: {
    setTheme (index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    registerTheme () {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setFontSize (fontSize) {
      if (this.themes) {
        this.defaultFontSize = fontSize
        this.themes.fontSize(fontSize + 'px')
      }
    },
    toggleTitleAndMenu () {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
      if (!this.ifTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting()
      }
    },
    prevPage () {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage () {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    showEpub () {
      this.book = new Epub(DOWNLOAD_URL)
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      this.rendition.display()
      this.themes = this.rendition.themes
      this.setFontSize(this.defaultFontSize)
      this.registerTheme()
      this.themes.select(this.defaultTheme)
    }
  },
  mounted () {
    this.showEpub()
  }
}
</script>
<style lang='scss' scoped>
@import 'assets/styles/global';
.ebook{
  position:relative;
  .read-wrapper{
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      z-index:100;
      width:100%;
      height: 100%;
      display: flex;
      .left{
        flex:0 0 px2rem(100);
      }
      .left:hover{
        background: gray;
        opacity: 0.5;
      }
      .center{
        flex:1;
      }
      .right{
        flex:0 0 px2rem(100);
      }
      .right:hover{
        background: gray;
        opacity: 0.5;
      }
    }
  }
}
</style>
