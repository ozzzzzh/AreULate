<script>
export default {
  onLaunch() {
    console.log('App Launch')
  },
  onShow() {
    console.log('App Show')
  },
  onHide() {
    console.log('App Hide')
  }
}
</script>

<style>
/* 苹果风格全局样式 */
page {
  background-color: #f2f2f7;
  min-height: 100vh;
  font-family: -apple-system, BlinkMacSystemFont, 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

view, text {
  box-sizing: border-box;
}

/* 去除小程序点击高亮 */
button::after {
  border: none;
}

view, button {
  -webkit-tap-highlight-color: transparent;
}

/* 滚动条隐藏 */
::-webkit-scrollbar {
  display: none;
  width: 0;
  height: 0;
}
</style>