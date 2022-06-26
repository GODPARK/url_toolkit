<template>
    <div>
      <v-card
          elevation="4"
          outlined
      >
        <v-toolbar
          elevation="0"
          outlined
        >
          <v-toolbar-title style="font-size:1rem;">
            Parameters.
          </v-toolbar-title>
          <div class="ml-4" :style="clipBoardResult.style">
            {{ clipBoardResult.text }}
          </div>
        </v-toolbar>
        <v-data-table
          dense
          :headers="paramsHeader"
          :items="paramsList"
          item-key="key"
          calculate-widths
          mobile-breakpoint="500"
          class="elevation-1 mt-1"
          :items-per-page="-1"
        >
          <template v-slot:[`item.key`]="{ item }">
            <v-card
              class="text-center ma-1"
              small
              dark
              @click="copyTextToClipBoard(item.key)"
              width="80"
            >
              <strong>
                {{ item.key }}
              </strong>
            </v-card>
          </template>
          <template v-slot:[`item.decode`]="{ item }">
            <v-card
              elevation="0"
              class="ma-1 pa-1"
              style="cursor: pointer;overflow: scroll;"
              @click="copyTextToClipBoard(decodeValueInTable(item))"
              width="280"
            >
              <div v-if="!isJsonString(decodeValueInTable(item))">
                {{ decodeValueInTable(item) }}
              </div>
              <div v-if="isJsonString(decodeValueInTable(item))">
                <vue-json-pretty
                  :data="stringParseToObject(item)"
                >
                </vue-json-pretty>
              </div>
            </v-card>
          </template>
          <template v-slot:[`item.value`]="{ item }">
            <v-card
              class="pa-1 ma-1"
              elevation="0"
              @click="copyTextToClipBoard(item.value)"
              style="cursor: pointer;"
              width="260"
            >
              {{ item.value }}
            </v-card>
          </template>
        </v-data-table>
      </v-card>
    </div>
</template>

<script>
import VueJsonPretty from 'vue-json-pretty';
import 'vue-json-pretty/lib/styles.css';

export default {
  name: 'ParamsInfo',
  components: {
    VueJsonPretty,
  },
  props: {
    pageInfo: {
      default: () => new URL(''),
    },
    testBool: {
      type: Boolean,
      default: () => false,
    },
  },
  computed: {
  },
  mounted() {
    this.makeParams(this.pageInfo.search);
  },
  methods: {
    isJsonString(str) {
      try {
        if (str === undefined || str === '') return false;
        const json = JSON.parse(str);
        return (typeof json === 'object');
      } catch (e) {
        return false;
      }
    },
    stringParseToObject(item) {
      try {
        const value = decodeURIComponent(item.value);
        if (value === item.value) {
          return {};
        }
        return JSON.parse(value);
      } catch {
        return {};
      }
    },
    makeParams(rawParams) {
      if (rawParams !== '') {
          const removeQuestionMark = rawParams.replace('?', '');
          const resultParamList = removeQuestionMark.split('&');

          resultParamList.forEach((element) => {
          const paramKeyValue = element.split('=');
          if (paramKeyValue.length === 2) {
              this.paramsList.push(
                {
                  key: paramKeyValue[0],
                  value: paramKeyValue[1],
                },
              );
          } else if (paramKeyValue.length === 1) {
              this.paramsList.push(
                {
                  key: paramKeyValue[0],
                  value: '',
                },
              );
          }
        });
      }
    },

    decodeValueInTable(item) {
      try {
        const value = decodeURIComponent(item.value);
        if (value === item.value) {
          return value;
        }
        return value;
      } catch {
        return '?';
      }
    },

    copyTextToClipBoard(copyText) {
      if (copyText !== undefined && copyText !== '') {
        if (navigator.clipboard) {
              navigator.clipboard.writeText(copyText).then(() => {
                  this.clipBoardResult.text = 'copy success!';
                  this.clipBoardResult.style.color = 'green';
              });
          } else {
              this.clipBoardResult.text = 'copy fail!';
              this.clipBoardResult.style.color = 'red';
          }

          setTimeout(() => {
              this.clipBoardResult.text = '';
              this.clipBoardResult.style.color = 'grey';
          }, 2000);
      }
    },
  },
  data: () => ({
    clipBoardResult: {
      text: '',
      style: {
        color: 'green',
      },
    },
    paramsList: [],
    paramsHeader: [
      {
        text: 'Key',
        align: 'left',
        sortable: true,
        value: 'key',
      },
      {
        text: 'Decode',
        align: 'left',
        sortable: false,
        value: 'decode',
      },
      {
        text: 'Value',
        align: 'left',
        sortable: false,
        value: 'value',
      },
    ],
  }),
};
</script>
