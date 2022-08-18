<script>
import Vue from "vue";
import VueAdminSidebar from "@/entry";

Vue.use(VueAdminSidebar);

export default Vue.extend({
  name: "ServeDev",
  data() {
    return {
      collapsed: {
        value: true,
        width: "55px",
      },
      menu: [
        {
          header: true,
          title: "Getting Started",
          hiddenOnCollapse: true,
        },
        {
          href: "/",
          title: "Installation",
          icon: "fa fa-download",
        },
        {
          href: "/basic-usage",
          title: "Basic Usage",
          icon: "fa fa-code",
        },
        {
          header: true,
          title: "Usage",
          hiddenOnCollapse: true,
        },
        {
          href: "/props",
          title: "Props",
          icon: "fa fa-cogs",
        },
        {
          href: "/events",
          title: "Events",
          icon: "fa fa-bell",
        },
        {
          href: "/styling",
          title: "Styling",
          icon: "fa fa-palette",
        },
        {
          header: true,
          title: "Example",
          hiddenOnCollapse: true,
        },
        {
          href: "/disabled",
          title: "Disabled page",
          icon: "fa fa-lock",
          disabled: true,
        },
        {
          title: "Badge",
          icon: "fa fa-cog",
          badge: {
            text: "new",
            class: "vsm--badge_default",
          },
        },
        {
          href: "/page",
          title: "Dropdown Page",
          icon: "fa fa-list-ul",
          child: [
            {
              href: "/page/sub-page-1",
              title: "Sub Page 01",
              icon: "fa fa-file-alt",
            },
            {
              href: "/page/sub-page-2",
              title: "Sub Page 02",
              icon: "fa fa-file-alt",
            },
          ],
        },
        {
          title: "Multiple Level",
          icon: "fa fa-list-alt",
          child: [
            {
              title: "page",
            },
            {
              title: "Level 2 ",
              child: [
                {
                  title: "page",
                },
                {
                  title: "Page",
                },
              ],
            },
            {
              title: "Page",
            },
            {
              title: "Another Level 2",
              child: [
                {
                  title: "Level 3",
                  child: [
                    {
                      title: "Page",
                    },
                    {
                      title: "Page",
                    },
                  ],
                },
              ],
            },
          ],
        },
      ],
    };
  },
});
</script>

<template>
  <div id="app">
    <vue-admin-sidebar
        :menu="menu"
        :relative="true"
        :collapsed="collapsed"
    >
      <template #header>
        <span>header</span>
      </template>
    </vue-admin-sidebar>

    <div class="main">
      <header class="header">
        <button @click="collapsed.value = !collapsed.value">Toggle Button</button>
      </header>
      <div class="content-wrapper">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  height: 100vh;
  display: flex;
  font-family: "Nunito", "Helvetica", sans-serif;

  .main {
    flex-grow: 1;
    height: 100vh;
    overflow: hidden;
    background-color: rgb(23, 23, 23);

    .header {
      height: 60px;
      background: inherit;
      display: flex;
      align-items: center;
      padding: 0 1rem;
      justify-content: flex-end;
    }

    .content-wrapper {
      height: calc(100vh - 60px);
      overflow: auto;
      padding: 1rem;
      background-color: inherit;
    }
  }
}
</style>
