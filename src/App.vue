<template>
  <v-app style="min-width:550px;">
    <v-app-bar
      app
      color="green darken-3"
      dense
      dark
    >
      <v-toolbar-title>
        <strong>
          Url Toolkit
        </strong>
      </v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row>
          <v-col cols="12">
            <main-info :pageInfo="pageInfo" />
          </v-col>
          <v-col cols="12">
            <url-encode />
          </v-col>
          <v-col
            cols="12"
            v-if="pageInfo.search !== undefined && pageInfo.search !== ''"
          >
            <params-info :pageInfo="pageInfo" :testBool="testBool"/>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

import MainInfo from './components/MainInfo.vue';
import UrlEncode from './components/UrlEncode.vue';
import ParamsInfo from './components/ParamsInfo.vue';

export default {
  name: 'App',

  components: {
    MainInfo,
    UrlEncode,
    ParamsInfo,
  },

  mounted() {
    this.getCurrentUrlInfo();
  },

  methods: {
    async getCurrentUrlInfo() {
      if (!this.testBool) {
        const queryOptions = {
          active: true,
          currentWindow: true,
        };
        /* eslint-disable */
        const tabs = await chrome.tabs.query(queryOptions);
        if (tabs.length > 0) {
          if (tabs[0].url !== undefined && tabs[0].url !== '') {
              this.pageInfo = new URL(tabs[0].url);
          }
        }
      } else {
        this.pageInfo = new URL(this.testData.url);
      }
    }
  },

  data: () => ({
    testBool: false,
    testData: {
      // url: 'https://www.google.com/search?q=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&rlz=1C5CHFA_enKR842KR842&oq=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&aqs=chrome..69i57j46i433i512j35i39j46i433i512j46i131i433i512j0i131i433i512l2j69i60.4597j0j4&sourceid=chrome&ie=UTF-8',
      url: 'https://github.com/GODPARK/url_toolkit/blob/master/src/components/ParamsInfo.vue',
    },
    pageInfo: {
    }
    //
  }),
};
</script>
