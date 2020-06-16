<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" clipped fixed app mini-variant>
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar clipped-left fixed app :elevation="1">
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />
      <v-spacer />
      <v-btn icon @click.stop="$vuetify.theme.dark = !$vuetify.theme.dark">
        <v-icon>mdi-invert-colors</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer padless>
      <v-card flat tile width="100%" class="text-center">
        <v-card-text>
          <v-btn
            v-for="link in links"
            :key="link.icon"
            :href="link.href"
            class="mx-4"
            icon
            :color="link.color"
            target="_blank"
          >
            <v-icon size="24px">{{ link.icon }}</v-icon>
          </v-btn>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-text> {{ new Date().getFullYear() }} — YunYouJun </v-card-text>
      </v-card>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      drawer: false,
      items: [
        {
          icon: 'mdi-apps',
          title: '主页',
          to: '/',
        },
        {
          icon: 'mdi-chart-bubble',
          title: '关于',
          to: '/inspire',
        },
      ],
      title: '闭嘴',
      links: [
        {
          href: 'https://github.com/YunYouJun/shut-up',
          color: 'black',
          icon: 'mdi-github',
        },
        {
          href: 'https://twitter.com/YunYouJun',
          color: 'light-blue',
          icon: 'mdi-twitter',
        },
        {
          href: 'https://www.yunyoujun.cn',
          color: 'indigo',
          icon: 'mdi-web',
        },
        {
          href: 'https://t.me/elpsycn',
          color: 'blue',
          icon: 'mdi-telegram',
        },
      ],
    }
  },
}
</script>
