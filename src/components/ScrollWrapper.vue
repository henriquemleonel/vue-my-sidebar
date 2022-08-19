<script>
export default {
  name: "ScrollWrapper",

  data () {
    return {
      position: [0, 0],
      direction: '',

      mark: {
        top: 50,
        bottom: 50
      },
    }
  },

  computed: {
    wrapperClass () {
      return [
          'vsm--scroll-wrapper',
          this.direction === 'down' ? 'scroll--down' : 'scroll--up'
      ]
    }
  },

  methods: {
    onScroll (e) {
      // const target = e.target
      this.position = [0, e.target.scrollTop]
      if (this.position[1] > this.mark.top) {
        this.direction = 'down'
      } else {
        this.direction = 'up'
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
      <button
          class="vsm--top-mark"
          v-if="direction === 'down'"
          @click="onScrollUp"
      >
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M5 16.929L6.41421 18.3432L12.0711 12.6863L17.7279 18.3432L19.1421 16.929L12.0711 9.85789L5 16.929Z" fill="currentColor" /><path d="M19 8H5V6H19V8Z" fill="currentColor" />
        </svg>
      </button>
    </transition>

    <slot />

    <transition name="appear-down">
      <button
          class="vsm--bottom-mark"
          v-if="direction === 'up'"
          @click="onScrollDown"
      >
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M5 7.41421L6.41421 6L12.0711 11.6569L17.7279 6L19.1421 7.41421L12.0711 14.4853L5 7.41421Z" fill="currentColor" /><path d="M19 16.3432H5V18.3432H19V16.3432Z" fill="currentColor" />
        </svg>
      </button>
    </transition>
  </div>
</template>