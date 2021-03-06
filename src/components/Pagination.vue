<template>
  <div id="pagination" class="pages-container">
    <ul class="pages">
      <li class="page" 
        :class="preHover ? 'mobile-hover' : ''" 
        @click="handleClickControl(-1)"
        @touchstart="preHover = true"
        @touchend="preHover = false"
      >
        {{pre}}
      </li>
      <li class="page" 
        v-for="(item, index) in pages" 
        :key="index"
        :class="{ellipsis: item === ellipsis, active: item === currentPage}"
        @click="handleClickActive(item)"
        @touchstart="addMobileHover(item, index)"
        @touchend="removeMobileHover(item, index)"
        ref="page"
      >
        <span>{{item}}</span>
      </li>
      <li class="page" 
        :class="nextHover ? 'mobile-hover' : ''" 
        @click="handleClickControl(+1)"
        @touchstart="nextHover = true"
        @touchend="nextHover = false"
      >
        {{next}}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Pagination',
  props: {
    totalPages: {
      type: Number,
      default: 20
    },
    initialPage: {
      type: Number,
      default: 1
    },
    ellipsis: {
      type: String,
      default: '···'
    },
    pre: {
      type: String,
      default: 'Prev'
    },
    next: {
      type: String,
      default: 'Next'
    },
    color: {
      type: String,
      default: '#2C3E50'
    },
    background: {
      type: String,
      default: '#fff'
    },
    weight: {
      type: String,
      default: 'normal'
    },
    font: {
      type: String,
      default: 'Avenir, Helvetica, Arial, sans-serif'
    }
  },
  data () {
    return {
      currentPage: this.initialPage,
      preHover: false,
      nextHover: false
    }
  },
  computed: {
    pages() {
      const c = this.currentPage
      const t = this.totalPages
      if (t <= 11) {
        const pages = []
        for (let i = 1; i <= t; i++) {
          pages.push(i)
        }
        return pages
      } else {
        let pages = []
        if (c <= 6) {
          pages = [1, 2, 3, 4, 5, 6, 7, 8, 9, this.ellipsis, t]
        } else if (c >= t - 5) {
          pages = [1, this.ellipsis, t-8, t-7, t-6, t-5, t-4, t-3, t-2, t-1, t]
        } else {
          pages = [1, this.ellipsis, c-3, c-2, c-1, c, c+1, c+2, c+3, this.ellipsis,t]
        }
        return pages
      }
    }
  },
  methods: {
    handleClickActive(item) {
      if (this.currentPage === item) return
      if (item === this.ellipsis) return
      this.currentPage = item
      this.$emit('changePage', this.currentPage)
    },
    handleClickControl(n) {
      if (this.currentPage === 1 && n === -1 || this.currentPage === this.totalPages && n === 1) return
      this.currentPage += n
      this.$emit('changePage', this.currentPage)
    },
    addMobileHover(item, index) {
      if (item !== this.currentPage) {
        this.$refs.page[index].className = "page mobile-hover"
      }
    },
    removeMobileHover(item, index) {
      if (item !== this.currentPage) {
        this.$refs.page[index].className = "page"
      }
    },
    setColor(color, background) {
      this.$el.style.setProperty('--theme-color', color)
      this.$el.style.setProperty('--background-color', background)
    },
    setWeight(weight) {
      if (weight === 'normal') {
        this.$el.style.setProperty('--border-width', '2px')
        this.$el.style.setProperty('--font-weight', '600')
        this.$el.style.setProperty('--ellips-size', '24px')
      }
      else if (weight === 'light') {
        this.$el.style.setProperty('--border-width', '1px')
        this.$el.style.setProperty('--font-weight', '400')
      }
    },
    setFont(font) {
      this.$el.style.setProperty('--font-family', font)
    }
  },
  mounted () {

    this.setColor(this.color, this.background)
    this.setFont(this.font)
    this.setWeight(this.weight)
  }
}
</script>

<style lang="stylus" scoped>
  .pages-container
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    --theme-color: #2C3E50
    --background-color: #fff
    --font-family: Avenir, Helvetica, Arial, sans-serif
    --border-width: 2px
    --font-weight: 600
    margin: 0
    padding: 0
    .pages
      position: relative
      padding: 20px 0
      text-align: center
      white-space: nowrap
      .page
        margin: 0 3px
        height: 38px
        min-width: 38px 
        background-color: var(--background-color)
        border-radius: 38px
        border: var(--border-width) solid var(--theme-color)
        color: var(--theme-color)
        display: inline-block
        line-height: 38px
        text-align: center
        font-size: 14px
        font-family: var(--font-family)
        font-weight: var(--font-weight)
        text-decoration: none
        cursor: pointer
        white-space: nowrap
        overflow: hidden
        text-overflow: ellipsis
        transition: background-color .1s
        &:first-child, 
        &:last-child
          padding: 0 16px
          margin: 0 12px
        @media(hover: hover) and (pointer: fine)
          &:hover
            background-color: var(--theme-color)
            border-color: var(--theme-color)
            color: var(--background-color)
            opacity: .5
      .mobile-hover
        background-color: var(--theme-color)
        border-color: var(--theme-color)
        color: var(--background-color)
        opacity: .5
      .active
        background-color: var(--theme-color)
        border-color: var(--theme-color)
        color: var(--background-color)
        &:hover
          opacity: 1
      .ellipsis
        background-color: transparent
        border-color: transparent
        font-size: 24px
        margin: 0 5px
        pointer-events: none
  </style>
