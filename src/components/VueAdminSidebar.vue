<script src="https://kit.fontawesome.com/3b510842b6.js" crossorigin="anonymous"></script>

<script>
import ScrollWrapper from "./ScrollWrapper.vue";
import SidebarMenuItem from "./SidebarMenuItem.vue";

export default {
    name: "VueAdminSidebar",
    components: {
      ScrollWrapper,
      SidebarMenuItem,
    },
    props: {
        menu: {
            type: Array,
            required: true,
        },
        collapsed: {
            type: Object,
            default: () => ({ value: true, width: "65px" }),
        },
        width: {
            type: String,
            default: "320px",
        },
        theme: {
          type: String,
          default: "",
        },
        showChild: {
            type: Boolean,
            default: false,
        },
        showOneChild: {
            type: Boolean,
            default: true,
        },
        rtl: {
            type: Boolean,
            default: false,
        },
        hideToggle: {
            type: Boolean,
            default: false,
        },
        hideHeaders: {
          type: Boolean,
          default: true,
        },
        disableHover: {
            type: Boolean,
            default: false,
        },

        // Classes
        sidebarClasses: {
            type: String,
            default: 'vsm--background'
        }
    },
    data() {
        return {
            mobileItem: null,
            mobileItemPos: 0,
            mobileItemHeight: 0,
            mobileItemTimeout: null,
            activeShow: null,
            parentHeight: 0,
            parentWidth: 0,
            parentOffsetTop: 0,
            parentOffsetLeft: 0,
        };
    },
    computed: {
        sidebarWidth() {
            return this.collapsed.value ? this.collapsed.width : this.width;
        },
        sidebarClass() {
            return [
                !this.collapsed.value ? "vsm_expanded" : "vsm_collapsed",
                this.theme ? `vsm_${this.theme}` : "",
                this.rtl ? "vsm_rtl" : "",
                this.sidebarClasses
                // this.relative ? "vsm_relative" : "",
            ];
        },
        mobileItemStyle() {
            return {
              /* from item
              *
              */
                item: [
                    { position: "absolute" },
                    { top: `${this.mobileItemPos}px` },
                    this.rtl ? { right: "0px" } : { left: "0px" },
                                        this.rtl && { direction: "rtl" },
                    this.rtl ? { "padding-right": this.sidebarWidth } : { "padding-left": this.sidebarWidth },
                  { "z-index": 0 },
                    { width: `${this.parentWidth - this.parentOffsetLeft}px` },
                    { "max-width": this.width },
                ],
                dropdown: [
                    { position: "absolute" },
                    { top: `${this.mobileItemHeight}px` },
                    { width: "100%" },
                    { height: "100%" },
                    {
                        "max-height": `${
                            this.parentHeight -
                            (this.mobileItemPos + this.mobileItemHeight) -
                            this.parentOffsetTop
                        }px`,
                    },
                    { "overflow-y": "auto" },
                ],
                background: [
                    { position: "absolute" },
                    { top: "0px" },
                    { left: "0px" },
                    { right: "0px" },
                    { width: "100%" },
                    { height: `${this.mobileItemHeight}px` },
                    { "z-index": -1 },
                ],
            };
        },
    },
    methods: {
        onScroll (e) {
          const target = e.target
          const scrollY = e.target.scrollTop
          if (scrollY > 50) {
            console.log('scrolling', e)
            target.classList.add('vsm--scroll_to-bottom')
          }
        },

        onMouseLeave() {
            this.unsetMobileItem(false, 300);
        },
        onMouseEnter() {
            if (this.collapsed.value) {
                if (this.mobileItemTimeout)
                    clearTimeout(this.mobileItemTimeout);
            }
        },

        onToggleClick() {
            this.collapsed.value = !this.collapsed.value;
            this.mobileItem = null;
            this.$emit("toggle-collapse", this.collapsed.value);
        },
        onActiveShow(item) {
            this.activeShow = item;
        },
        onItemClick(event, item, node) {
            this.$emit("item-click", event, item, node);
        },

        setMobileItem({ item, itemEl }) {
            if (this.mobileItem === item) return;
            const sidebarTop = this.$el.getBoundingClientRect().top;
            const itemLinkEl = itemEl.children[0];
            const { top, height } = itemLinkEl.getBoundingClientRect();

            let positionTop = top - sidebarTop;
            this.initParentOffsets();
            this.mobileItem = item;
            this.mobileItemPos = positionTop;
            this.mobileItemHeight = height;
        },
        unsetMobileItem(immediate, delay = 800) {
            if (!this.mobileItem) return;
            if (this.mobileItemTimeout) clearTimeout(this.mobileItemTimeout);
            if (immediate) {
                this.mobileItem = null;
                return;
            }
            this.mobileItemTimeout = setTimeout(() => {
                this.mobileItem = null;
            }, delay);
        },
        initParentOffsets() {
            let {
                top: sidebarTop,
                left: sidebarLeft,
                right: sidebarRight,
            } = this.$el.getBoundingClientRect();
            let parent = this.relative
                ? this.$el.parentElement
                : document.documentElement;
            this.parentHeight = parent.clientHeight;
            this.parentWidth = parent.clientWidth;
            if (this.relative) {
                let {
                    top: parentTop,
                    left: parentLeft,
                } = parent.getBoundingClientRect();
                this.parentOffsetTop =
                    sidebarTop - (parentTop + parent.clientTop);
                this.parentOffsetLeft = this.rtl
                    ? this.parentWidth -
                      sidebarRight +
                      (parentLeft + parent.clientLeft)
                    : sidebarLeft - (parentLeft + parent.clientLeft);
            } else {
                this.parentOffsetTop = sidebarTop;
                this.parentOffsetLeft = this.rtl
                    ? this.parentWidth - sidebarRight
                    : sidebarLeft;
            }
        },
        onItemUpdate(newItem, item) {
            if (item === this.mobileItem) {
                this.mobileItem = newItem;
            }
            if (item === this.activeShow) {
                this.activeShow = newItem;
            }
        },

        onResize() {
            if (window.innerWidth <= 992) {
                this.collapsed.value = true;
            } 
            // else {
            //     // this.isOnMobile = false;
            //     this.collapsed.value = false;
            // }
        },
    },
    mounted() {
        this.onResize();
        window.addEventListener("resize", this.onResize);
    },
    provide() {
        return {
            emitActiveShow: this.onActiveShow,
            emitItemClick: this.onItemClick,
            emitItemUpdate: this.onItemUpdate,
        };
    },
};
</script>

<template>
  <div>
    <div
        class="vsm-sidebar-menu"
        :class="sidebarClass"
        :style="`--width:${width}; --widthCollapsed:${collapsed.width}`"
        @mouseleave="onMouseLeave"
        @mouseenter="onMouseEnter"
    >
      <div class="vsm--area logo-area">
        <slot name="logo">
          <div class="vsm--item">
            <div class="vsm--icon-area">
              <svg class="vsm--icon" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path fill-rule="evenodd" clip-rule="evenodd" d="M4 5C4 3.34315 5.34315 2 7 2H17C18.6569 2 20 3.34315 20 5V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V5ZM13 4H17C17.5523 4 18 4.44772 18 5V13H13V4ZM13 15V20H17C17.5523 20 18 19.5523 18 19V15H13ZM11 4H7C6.44771 4 6 4.44772 6 5V8H11V4ZM6 19V10H11V20H7C6.44772 20 6 19.5523 6 19Z" fill="currentColor" />
              </svg>
            </div>
          </div>
        </slot>

        <button
            v-if="!hideToggle"
            class="vsm--toggle-btn"
            :class="[{'collapsed': collapsed.value}, {'vsm--toggle-btn_slot' : $slots['toggle-icon']}]"
            @click="onToggleClick"
        >
          <slot name="toggle-icon">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512" fill="currentColor">
              <!--! Font Awesome Pro 6.1.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
              <path d="M192 448c-8.188 0-16.38-3.125-22.62-9.375l-160-160c-12.5-12.5-12.5-32.75 0-45.25l160-160c12.5-12.5 32.75-12.5 45.25 0s12.5 32.75 0 45.25L77.25 256l137.4 137.4c12.5 12.5 12.5 32.75 0 45.25C208.4 444.9 200.2 448 192 448z"/>
            </svg>
          </slot>
        </button>
      </div>

      <div
          class="vsm--area"
          :class="[{'area--hidden' : !$slots['header']}]"
      >
        <slot name="header" />
      </div>

      <ScrollWrapper
          :style="collapsed.value && [rtl ? {'margin-left': '-17px'} : {'margin-right': '0'}]"
      >
        <div class="vsm--list" :style="collapsed.value && {'width': collapsed.width}">
          <sidebar-menu-item
              v-for="(item, index) in menu"
              :key="index"
              :item="item"
              :is-collapsed="collapsed.value"
              :active-show="activeShow"
              :show-child="showChild"
              :show-one-child="showOneChild"
              :hide-headers="hideHeaders"
              :rtl="rtl"
              :mobile-item="mobileItem"
              :disable-hover="disableHover"
              @set-mobile-item="setMobileItem"
              @unset-mobile-item="unsetMobileItem"
          >
            <slot name="dropdown-icon" />
          </sidebar-menu-item>
        </div>

        <div v-if="collapsed.value" class="vsm--mobile-item" :style="mobileItemStyle.item">
          <sidebar-menu-item
              v-if="mobileItem"
              :item="mobileItem"
              :is-mobile-item="true"
              :mobile-item-style="mobileItemStyle"
              :is-collapsed="collapsed.value"
              :show-child="showChild"
              :rtl="rtl"
              :disable-hover="disableHover"
          >
            <slot name="dropdown-icon" />
          </sidebar-menu-item>

          <transition name="slide-animation">
            <div
                v-if="mobileItem"
                class="vsm--mobile-bg"
                :style="mobileItemStyle.background"
            />
          </transition>
        </div>
      </ScrollWrapper>

      <div
          class="vsm--area"
          :class="[{'slot--mask' : $slots['footer']}]"
      >
        <slot name="footer" />
      </div>
    </div>
    <div class="v-overlay" @click="onToggleClick"></div>
  </div>
</template>

<style lang="scss">
@import "../scss/vue-admin-sidebar";
</style>
