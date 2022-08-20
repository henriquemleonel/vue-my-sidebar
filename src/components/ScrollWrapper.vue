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
        bottom: 50,
      },

      scrollY: 0,
      scrollX: 0,
      isDownScroll: false,

      boundingRect: null,
    }
  },

  computed: {
    wrapperClass () {
      return [
          'vsm--scroll-wrapper',
          this.isDownScroll ? 'scroll--down' : 'scroll--up'
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
      } else return this.isDownScroll && this.scrollY < this.anchors.bottom;
    },

    getChildrenHeight () {
      const slotSize = this.$children.length || 0
      if (slotSize) {
        return this.$el.children[1].getBoundingClientRect().height
      }
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
      this.scrollY = e.target.scrollTop
      if (!this.boundingRect) {
        this.boundingRect = e.target.getBoundingClientRect()
      }

      // const percent = Math.round(this.scrollY / (this.boundingRect.height) * 100)
      // console.log('percent', percent)
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
    }
  }
}
</script>

<template>
  <div
      ref="scroller"
      :class="wrapperClass"
      @scroll="onScroll"
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

    <transition name="appear-down">
      <div class="vsm--scroll-anchor bottom-anchor" v-if="!isBottomAnchorHidden">
        <button
            class="vsm--scroll-setter"
            @click="onScrollDown"
        >
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M5 7.41421L6.41421 6L12.0711 11.6569L17.7279 6L19.1421 7.41421L12.0711 14.4853L5 7.41421Z" fill="currentColor" /><path d="M19 16.3432H5V18.3432H19V16.3432Z" fill="currentColor" />
          </svg>
        </button>
      </div>
    </transition>
  </div>
</template>