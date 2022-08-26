<script>
export default {
  name: "ScrollWrapper",

  props: {
    hideTopAnchor: {
      type: Boolean,
      default: false
    },
    hideBottomAnchor: {
      type: Boolean,
      default: true
    }
  },

  data () {
    return {
      anchors: {
        top: 50,
        bottom: 80,
      },

      scrollY: 0,
      scrollX: 0,
      scrollPercent: 0,
      scrollChildrenOffset: 0,
      isScrolling: false,
      isDownScroll: false,

      boundingRect: null,
    }
  },

  computed: {
    wrapperClass () {
      return [
          'vsm--scroll-wrapper',
          this.isDownScroll && this.scrollChildrenOffset > 0 ? 'scroll--down' : '',
          !this.isDownScroll && this.scrollChildrenOffset > 0 ? 'scroll--up' : '',
      ]
    },

    isTopAnchorHidden () {
      if (this.hideTopAnchor) {
        return true
      } else if (this.scrollY >  this.boundingRect?.height) {
        console.log('overflow')
        return false
      } else return !this.isDownScroll && this.scrollY < this.anchors.top;
    },

    isBottomAnchorHidden () {
      if (this.hideBottomAnchor) {
        return true
      } else if (this.scrollY >  this.boundingRect?.height) {
        console.log('overflow')
        return false
      } else return this.isDownScroll && this.scrollY < this.anchors.bottom;
    }
  },

  watch: {
    'scrollY' (currentY, oldY) {
      this.isDownScroll = currentY > 0 && currentY > oldY
    },
    '$el' (value) {
      console.log('binding h', value)
    }
  },

  methods: {

    onScroll (e) {
      this.isScrolling = true
      this.scrollY = e.target.scrollTop

      if (!this.boundingRect) {
        this.boundingRect = e.target.getBoundingClientRect()
      }

      this.scrollPercent = this.getScrollPercent()
      console.log('percent', this.scrollPercent)

      if (this.isDownScroll) {
        // console.log('down')
      } else {
        // console.log('up')
      }
    },

    onScrollUp () {
      this.$refs.scroller.scrollTo({ top: 0, behavior: "smooth"})
    },

    onScrollDown () {
      const scrlHght = this.$refs.scroller.scrollHeight
      this.$refs.scroller.scrollTo({ top: scrlHght, behavior: "smooth"})
    },

    getScrollPercent () {
      const children = this.$refs.scroller.children
      const accHeight = Array.from(children).reduce((acc, el) => {
        if (!el.classList.contains('vsm--scroll-anchor')) {
          return acc + el.clientHeight
        }
        return acc
      }, 0)

      this.scrollChildrenOffset = accHeight - this.boundingRect.height
      return Math.round(this.scrollY / (this.scrollChildrenOffset) * 100)
    }
  }
}
</script>

<template>
  <div
      ref="scroller"
      :class="wrapperClass"
      @scroll="onScroll"
      @scroll.stop="isScrolling = false"
  >
    <transition name="appear-up">
      <div class="vsm--scroll-anchor top-anchor">
        <button
            class="vsm--scroll-setter"
            v-if="!isTopAnchorHidden"
            @click="onScrollUp"
        >
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M5 16.929L6.41421 18.3432L12.0711 12.6863L17.7279 18.3432L19.1421 16.929L12.0711 9.85789L5 16.929Z" fill="currentColor" /><path d="M19 8H5V6H19V8Z" fill="currentColor" />
          </svg>
        </button>
      </div>
    </transition>

    <slot />

<!--    <transition name="appear-down">-->
<!--      <div class="vsm&#45;&#45;scroll-anchor bottom-anchor" v-if="!isBottomAnchorHidden">-->
<!--        <button-->
<!--            class="vsm&#45;&#45;scroll-setter"-->
<!--            @click="onScrollDown"-->
<!--        >-->
<!--          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">-->
<!--            <path d="M5 7.41421L6.41421 6L12.0711 11.6569L17.7279 6L19.1421 7.41421L12.0711 14.4853L5 7.41421Z" fill="currentColor" /><path d="M19 16.3432H5V18.3432H19V16.3432Z" fill="currentColor" />-->
<!--          </svg>-->
<!--        </button>-->
<!--      </div>-->
<!--    </transition>-->
  </div>
</template>