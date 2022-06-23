<template>
  <v-app style="min-width:550px;min-height: 800px;">
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
      <v-footer padless>
        <v-col
          class="text-center"
          cols="12"
        >
          Made by PJH.
        </v-col>
      </v-footer>
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
    testBool: true,
    testData: {
      url: 'https://www.google.com/search?q=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&rlz=1C5CHFA_enKR842KR842&oq=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&aqs=chrome..69i57j46i433i512j35i39j46i433i512j46i131i433i512j0i131i433i512l2j69i60.4597j0j4&sourceid=chrome&ie=UTF-8',
      // url: 'https://github.com/GODPARK/url_toolkit/blob/master/src/components/ParamsInfo.vue',
      /* eslint-disable */
      // url: 'https://www.google.com/search?q=%EC%9D%B4%EB%A0%87%EA%B2%8C+%EA%B8%B4+%EA%B2%80%EC%83%89%EC%96%B4%EB%A5%BC+%EA%B2%80%EC%83%89%ED%95%98%EB%A9%B4+%EC%96%B4%EB%96%BB%EA%B2%8C+%EB%90%A0%EA%B9%8C%EC%9A%94+%EA%B7%B8%EB%9E%98%EC%84%9C+%EA%B8%B8%EB%A9%B4+%ED%99%94%EB%A9%B4%EC%9D%B4+%EB%84%88%EB%AC%B4+%EA%B8%B8%EC%96%B4%EC%A0%B8%EC%84%9C+%EA%B0%92%EC%9D%B4+%EB%82%98%EC%98%A4%EC%A7%80+%EC%95%8A%EC%9D%84+%EC%88%98+%EC%9E%88%EC%97%87%EC%9A%94&rlz=1C5CHFA_enKR842KR842&oq=%EC%9D%B4%EB%A0%87%EA%B2%8C+%EA%B8%B4+%EA%B2%80%EC%83%89%EC%96%B4%EB%A5%BC+%EA%B2%80%EC%83%89%ED%95%98%EB%A9%B4+%EC%96%B4%EB%96%BB%EA%B2%8C+%EB%90%A0%EA%B9%8C%EC%9A%94+%EA%B7%B8%EB%9E%98%EC%84%9C+%EA%B8%B8%EB%A9%B4+%ED%99%94%EB%A9%B4%EC%9D%B4+%EB%84%88%EB%AC%B4+%EA%B8%B8%EC%96%B4%EC%A0%B8%EC%84%9C+%EA%B0%92%EC%9D%B4+%EB%82%98%EC%98%A4%EC%A7%80+%EC%95%8A%EC%9D%84+%EC%88%98+%EC%9E%88%EC%97%87%EC%9A%94&aqs=chrome..69i57.22897j0j7&sourceid=chrome&ie=UTF-8'
      /* eslint-disable */
      // url: 'https://www.google.com/search?q=%EC%9D%B4%EB%A0%87%EA%B2%8C+%EA%B8%B4+%EA%B2%80%EC%83%89%EC%96%B4%EB%A5%BC+%EA%B2%80%EC%83%89%ED%95%98%EB%A9%B4+%EC%96%B4%EB%96%BB%EA%B2%8C+%EB%90%A0%EA%B9%8C%EC%9A%94+%EA%B7%B8%EB%9E%98%EC%84%9C+%EA%B8%B8%EB%A9%B4+%ED%99%94%EB%A9%B4%EC%9D%B4+%EB%84%88%EB%AC%B4+%EA%B8%B8%EC%96%B4%EC%A0%B8%EC%84%9C+%EA%B0%92%EC%9D%B4+%EB%82%98%EC%98%A4%EC%A7%80+%EC%95%8A%EC%9D%84+%EC%88%98+%EC%9E%88%EC%97%87%EC%9A%94&rlz=1C5CHFA_enKR842KR842&oq=%EC%9D%B4%EB%A0%87%EA%B2%8C+%EA%B8%B4+%EA%B2%80%EC%83%89%EC%96%B4%EB%A5%BC+%EA%B2%80%EC%83%89%ED%95%98%EB%A9%B4+%EC%96%B4%EB%96%BB%EA%B2%8C+%EB%90%A0%EA%B9%8C%EC%9A%94+%EA%B7%B8%EB%9E%98%EC%84%9C+%EA%B8%B8%EB%A9%B4+%ED%99%94%EB%A9%B4%EC%9D%B4+%EB%84%88%EB%AC%B4+%EA%B8%B8%EC%96%B4%EC%A0%B8%EC%84%9C+%EA%B0%92%EC%9D%B4+%EB%82%98%EC%98%A4%EC%A7%80+%EC%95%8A%EC%9D%84+%EC%88%98+%EC%9E%88%EC%97%87%EC%9A%94&aqs=chrome..69i57.22897j0j7&sourceid=chrome&ie=UTF-8&hwowo=%7B%20%20%20%20%20%22search%22%20%3A%20%7B%20%20%20%20%20%20%20%20%20%22searchWord%22%3A%20%22%EA%B2%80%EC%83%89%EC%9D%84%20%ED%96%88%EB%8B%A4~%20%EB%88%84%EA%B5%AC%EC%99%80%20%EB%A7%8C%EB%82%98~%22%2C%20%20%20%20%20%20%20%20%20%22searchPlatform%22%3A%20%22google%22%20%20%20%20%20%7D%20%7D&gg=%7B%20%09%22id%22%20%3A%2015%2C%20%09%22title%22%20%3A%20%22update%20api%20test%22%2C%20%09%22pid%22%20%3A%2011%2C%20%09%22isLeaf%22%20%3A%201%2C%20%09%22content%22%20%3A%20%22my%20content%20test%20edit%22%2C%20%09%22result%22%20%3A%201%2C%20%09%22end%22%3A%20%222020-07-07%22%2C%20%09%22status%22%20%3A%201%20%7D&ehieh=%7B%20%09%22id%22%20%3A%2015%2C%20%09%22title%22%20%3A%20%22update%20api%20test%22%2C%20%09%22pid%22%20%3A%2011%2C%20%09%22isLeaf%22%20%3A%201%2C%20%09%22content%22%20%3A%20%22my%20content%20test%20edit%22%2C%20%09%22result%22%20%3A%201%2C%20%09%22end%22%3A%20%222020-07-07%22%2C%20%09%22status%22%20%3A%201%20%7D'
    },
    pageInfo: {
    }
    //
  }),
};
</script>
