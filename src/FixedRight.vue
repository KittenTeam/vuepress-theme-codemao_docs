<template>
  <transition name="fade">
    <div class="fixed-right"
      v-show="show">
      <div v-if="useScrollNav" class="table-of-contents">
        <ul class="main-list">
          <li>
            <a href="javascript:;">{{ title }}</a>
            <ul ref="subScrollList" class="sub-list">
              <li v-for="v of level2data"
                :class="{ active: `#${v.slug}` === currentAnchor }"
                :key="v.title">
                <a @click.prevent="scrollToAnchor(v.slug)"
                  :href="`#${v.slug}`">{{ v.title }}</a>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <div class="scroll-to-top"
        @click="scrollTop()">
        <div class="icon"></div>
      </div>
    </div>
  </transition>
</template>
<script>
import { throttle, scrollTop, getElementTop } from './util'
export default {
  name: 'FixedRight',
  data() {
    return {
      show: false,
      currentAnchor: ''
    }
  },
  computed: {
    useScrollNav() {
      return this.$page.frontmatter.useScrollNav ||
        this.$themeLocaleConfig.useScrollNav ||
        this.$site.themeConfig.useScrollNav ||
        false
    },
    level2data() {
      if (!this.$page.headers) return []
      return this.$page.headers.filter(v => v.level === 2)
    },
    title() {
      return this.$page.title
    }
  },
  mounted() {
    const hanlderScroll = throttle(this.hanlderScroll, 300)
    window.addEventListener('scroll', hanlderScroll)
    this.$once('hook:beforeDestroy', () => {
      window.removeEventListener('scroll', hanlderScroll)
    })
  },
  methods: {
    hanlderScroll() {
      if (this.lock) return
      this.heartBeat()
      if (this.lastBeatTime) clearTimeout(this.lastBeatTime)
      this.lastBeatTime = setTimeout(this.heartBeat, 300)
    },
    heartBeat() {
      this.shouldShow()
      if (this.useScrollNav) {
        this.subScrollFix()
        this.setActiveHash()
      }
    },
    shouldShow(e) {
      const scrollTop = Math.max(
        window.pageYOffset,
        document.documentElement.scrollTop,
        document.body.scrollTop
      )
      this.show = scrollTop > 200
    },
    setActiveHash () {
      if (!this.show) return
      const anchors = [].slice.call(document.querySelectorAll('h2 .header-anchor'))

      const scrollTop = Math.max(
        window.pageYOffset,
        document.documentElement.scrollTop,
        document.body.scrollTop
      )

      for (let i = 0; i < anchors.length; i++) {
        const anchor = anchors[i]
        const nextAnchor = anchors[i + 1]

        /* eslint-disable */
        const isActive = i === 0 && scrollTop === 0 ||
        (scrollTop >= anchor.parentElement.offsetTop &&
        (!nextAnchor || scrollTop < nextAnchor.parentElement.offsetTop - 10))
        /* eslint-enable */
        if (isActive) {
          this.currentAnchor = decodeURIComponent(anchor.hash)
          return
        }
      }
    },
    subScrollFix() {
      const subScrollList = this.$refs.subScrollList
      const active = subScrollList ? subScrollList.querySelector('.active') : null
      if (active) {
        const offsetTop = active.offsetTop
        scrollTop({ el: subScrollList, top: offsetTop > 150 ? offsetTop - 150 : 0 })
      }
    },
    scrollTop,
    findAnchor(anchor) {
      return document.getElementById(decodeURI(anchor))
    },
    scrollToAnchor(anchor) {
      this.lock = true
      const el = this.findAnchor(anchor)
      if (el) {
        const top = getElementTop(el)
        scrollTop({ top: top + 10 })
      }
      setTimeout(() => {
        this.lock = false
        this.hanlderScroll()
      }, 600)
    },
    reset() {
      this.show = false
      this.currentAnchor = ''
    }
  }
}
</script>
<style lang="stylus" scoped>
@import './styles/config.styl'

.fixed-right
  bottom 100px
  left calc(50% + 7.5rem + 370px)
  position fixed
  @media (max-width: $MDcomputer)
    &
      left calc(80% + 7.5rem)
.table-of-contents
  width 200px
  .no-page-nav &
    display none
  a
    font-size 14px
    color #666
  .main-list
    position relative
    margin-top 0
    padding-left 0
    > li.active, .sub-list > li.active
      &:after
        display block
    &:after, &:before
      content ''
      position absolute
      width 8px
      height 8px
      border-radius 50%
      border 2px solid #eaecef
      background-color white
      z-index 10
    &:before
      left 14px
      top 12px
    &:after
      left 14px
      top 100%
      margin-top 5px
    > li
      position relative
      &:before
        left 20px
        top 14px
        content ''
        position absolute
        width 1px
        height 100%
        background-color #eaecef
      > a
        font-size 16px
        margin-left 40px
        color #333
    .sub-list
      position relative
      max-height 300px
      overflow-y scroll
      padding-left 40px
      &::-webkit-scrollbar
        display none
      > li
        position relative
        &:after
          content ''
          position absolute
          width 8px
          height 8px
          border-radius 50%
          background-color $accentColor
          left -23px
          top 50%
          margin-top -2px
          display none
      .active a
        color $accentColor
.scroll-to-top
  border-radius 4px
  background-color rgba(0, 0, 0, 0.45)
  overflow hidden
  margin-left 1em
  margin-top 50px
  height 40px
  width 40px
  cursor pointer
  transition opacity 0.3s cubic-bezier(0.645, 0.045, 0.355, 1)
  &:hover
    background-color rgba(0, 0, 0, 0.65)
.icon
  margin 12px auto
  width 14px
  height 16px
  background url('./images/toTop.png') 100% / 100% no-repeat
</style>
