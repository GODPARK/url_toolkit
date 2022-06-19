<template>
  <v-app style="min-width:400px;">
    <v-app-bar
      app
      color="green darken-3"
      dense
      dark
    >
      <v-toolbar-title>Url Toolkit</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row>
          <v-col cols="12">
            <main-info :testBool="testBool" :testData="testData" />
          </v-col>
          <v-col>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

import MainInfo from './components/MainInfo.vue';

export default {
  name: 'App',

  components: {
    MainInfo,
  },

  data: () => ({
    testBool: false,
    testData: {
      url: 'https://www.google.com/search?q=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&rlz=1C5CHFA_enKR842KR842&oq=%EC%95%88%EB%85%95%ED%95%98%EC%84%B8%EC%9A%94&aqs=chrome..69i57j46i433i512j35i39j46i433i512j46i131i433i512j0i131i433i512l2j69i60.4597j0j4&sourceid=chrome&ie=UTF-8',
    },
    //
  }),
};
</script>
