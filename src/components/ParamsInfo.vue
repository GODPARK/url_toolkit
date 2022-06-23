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
          <v-switch
            class="mt-5 ml-3"
            v-model="editBool"
            inset
            prepend-icon="mdi-pencil"
          ></v-switch>
          <div :style="clipBoardResult.style">
            {{ clipBoardResult.text }}
          </div>
          <v-spacer />
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="success"
                small
                @click="redirectAction()"
                v-bind="attrs"
                v-on="on"
              >
                GO
              </v-btn>
            </template>
            <span>new tab or browser open</span>
          </v-tooltip>
        </v-toolbar>
        <v-card
          v-if="editBool"
          class="ma-4"
          elevation="0"
          outlined
          color="grey lighten-3"
        >
          <div class="ml-3 mt-1" :style="newParamResult.style">
            {{ newParamResult.text }}
          </div>
          <v-container>
            <v-row>
              <v-col cols="4">
                <v-text-field
                  v-model="newParamKey"
                  label="Key"
                  dense
                  outlined
                  single-line
                  hide-details="true"
                >
                </v-text-field>
              </v-col>
              <v-col>
                <v-text-field
                  v-model="newParamValue"
                  label="Value"
                  dense
                  outlined
                  single-line
                  hide-details="true"
                >
                </v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <v-card-actions>
            <v-btn block small outlined @click="newParamAdd()"> ADD </v-btn>
          </v-card-actions>
        </v-card>
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
              :color="newParamColor(item)"
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
              style="cursor: pointer;"
              @click="copyTextToClipBoard(decodeValueInTable(item))"
              width="260"
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
              v-if="!editBool"
              class="pa-1 ma-1"
              elevation="0"
              @click="copyTextToClipBoard(item.value)"
              style="cursor: pointer;"
              width="270"
            >
              {{ item.value }}
            </v-card>
            <v-text-field
              v-if="editBool"
              v-model="item.value"
              label="value"
              dense
              outlined
              single-line
              hide-details="true"
              prepend-icon="mdi-content-paste"
              class="ma-1"
              @click:prepend="copyTextToClipBoard(item.value)"
              :hint="item.value"
            ></v-text-field>
          </template>
          <template v-slot:[`item.control`]="{ item }">
            <v-btn x-small text v-if="editBool" @click="deleteItemInList(item)">
              <v-icon>
                mdi-delete-outline
              </v-icon>
            </v-btn>
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
    newParamColor(item) {
      if (item.isNew) {
        return 'error';
      }
      return 'secondary';
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
                  isEdit: false,
                  isNew: false,
                },
              );
          } else if (paramKeyValue.length === 1) {
              this.paramsList.push(
                {
                  key: paramKeyValue[0],
                  value: '',
                  isEdit: false,
                  isNew: false,
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
          return '';
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

    deleteItemInList(item) {
      const filteredList = this.paramsList.filter((element) => element.key !== item.key);
      this.paramsList = filteredList;
    },

    newParamAdd() {
      if (this.newParamKey !== undefined && this.newParamKey !== null && this.newParamKey !== '') {
        const addValue = {
          key: '',
          value: '',
          isEdit: false,
          isNew: true,
        };
        addValue.key = this.newParamKey;
        if (this.newParamValue !== undefined && this.newParamValue !== null) {
          addValue.value = this.newParamValue;
        }
        let isAdd = true;
        this.paramsList.forEach((element) => {
          if (element.key === addValue.key) {
            isAdd = false;
          }
        });
        if (isAdd) {
          this.paramsList.push(addValue);
          this.newParamKey = '';
          this.newParamValue = '';
          this.newParamResult.text = 'new parameters add success';
          this.newParamResult.style.color = 'green';
        } else {
          this.newParamResult.text = 'key is duplicated';
          this.newParamResult.style.color = 'red';
        }
        setTimeout(() => {
            this.newParamResult.text = '';
            this.newParamResult.style.color = 'gray';
        }, 2500);
      }
    },

    makeRedirectUrl() {
      let baseUrl = this.pageInfo.origin + this.pageInfo.pathname;
      if (this.paramsList.length === 0) {
        return baseUrl;
      }
      baseUrl = `${baseUrl}?`;
      let index = 0;
      this.paramsList.forEach((element) => {
        baseUrl = `${baseUrl}${element.key}=${element.value}`;

        if (index + 1 < this.paramsList.length) {
          baseUrl += '&';
        }
        index += 1;
      });
      return baseUrl;
    },

    async redirectAction() {
      const url = this.makeRedirectUrl();
      if (this.testBool) {
        window.open(url, '_blank');
      } else {
        /* eslint-disable */
        await chrome.tabs.create({
          url: url
        });
      }
    },
  },
  data: () => ({
    editBool: false,
    newParamKey: '',
    newParamValue: '',
    newParamResult: {
      text: '',
      style: {
        color: 'green',
      },
    },
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
      {
        text: 'Control',
        align: 'left',
        sortable: false,
        value: 'control',
      },
    ],
  }),
};
</script>
