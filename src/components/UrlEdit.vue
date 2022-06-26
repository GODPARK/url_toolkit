<template>
    <div>
      <v-card
        elevation="4"
        outlined
      >
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="success"
            @click="redirectAction()"
          >
            GO
          </v-btn>
        </v-card-actions>
        <v-divider />
        <v-chip
          class="ml-2 mt-3"
          color="light-green lighten-4"
          label
        >
          <strong>
            URI.
          </strong>
        </v-chip>
        <v-card
          class="mr-2 ml-2 mt-1 pa-2 mb-3"
          color="grey lighten-2"
          style="overflow:auto;white-space: nowrap;"
        >
          <v-btn text small @click="copyTextToClipBoard(editUrlTotal)">
            <v-icon small>mdi-content-paste</v-icon>
          </v-btn>
          <strong>
            {{ editUrlTotal }}
          </strong>
        </v-card>
        <v-container>
          <v-row>
            <v-col cols="4">
              <v-text-field
                v-model="editPageInfo.protocol"
                @input="showEditUrl(editPageInfo)"
                label="protocol"
              >
              </v-text-field>
            </v-col>
            <v-col cols="8">
              <v-text-field
                v-model="editPageInfo.hostname"
                @input="showEditUrl(editPageInfo)"
                label="hostname"
              >
              </v-text-field>
            </v-col>
            <v-col cols="4">
              <v-text-field
                v-model="editPageInfo.port"
                @input="showEditUrl(editPageInfo)"
                label="port"
              >
              </v-text-field>
            </v-col>
            <v-col cols="8">
              <v-text-field
                v-model="editPageInfo.pathname"
                @input="showEditUrl(editPageInfo)"
                label="pathname"
              >
              </v-text-field>
            </v-col>
          </v-row>
        </v-container>
        <v-chip label color="light-green lighten-4" class="ml-2 mt-2">
          <strong>
            Parameters.
          </strong>
        </v-chip>
        <v-card
          class=""
          elevation="0"
        >
          <div class="ml-3 mt-1" :style="newParamResult.style">
            {{ newParamResult.text }}
          </div>
          <v-container>
            <v-row>
              <v-col cols="3">
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
              <v-col cols="2">
                <v-btn block outlined @click="newParamAdd()"> ADD </v-btn>
              </v-col>
            </v-row>
          </v-container>
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
              :color="editParamColor(item)"
              class="text-center ma-1"
              small
              dark
              width="80"
              @click="copyTextToClipBoard(item.key)"
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
              width="220"
              @click="copyTextToClipBoard(decodeValueInTable(item))"
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
            <v-text-field
              v-model="item.value"
              label="value"
              dense
              outlined
              single-line
              hide-details="true"
              prepend-icon="mdi-content-paste"
              @click:prepend="copyTextToClipBoard(item.value)"
              class="ma-1"
              :hint="item.value"
            ></v-text-field>
          </template>
          <template v-slot:[`item.control`]="{ item }">
            <v-btn x-small text
              @click="deleteItemInList(item)"
            >
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
  name: 'UrlEdit',
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
  computed: {},
  mounted() {
    this.makeParams(this.pageInfo.search);
    this.copyPageInfo(this.pageInfo);
  },
  methods: {
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
                  valueCopy: paramKeyValue[1],
                  isNew: false,
                },
              );
          } else if (paramKeyValue.length === 1) {
              this.paramsList.push(
                {
                  key: paramKeyValue[0],
                  value: '',
                  valueCopy: '',
                  isNew: false,
                },
              );
          }
        });
      }
    },
    copyPageInfo(info) {
      this.editPageInfo.protocol = info.protocol;
      this.editPageInfo.pathname = info.pathname;
      this.editPageInfo.hostname = info.hostname;
      this.editPageInfo.port = info.port;
      this.showEditUrl(this.editPageInfo);
    },
    showEditUrl(pageInfo) {
      if (pageInfo.port !== undefined && pageInfo.port !== '') {
        this.editUrlTotal = `${pageInfo.protocol}//${pageInfo.hostname}:${pageInfo.port}${pageInfo.pathname}`;
      } else {
        this.editUrlTotal = `${pageInfo.protocol}//${pageInfo.hostname}${pageInfo.pathname}`;
      }
    },
    deleteItemInList(item) {
      const filteredList = this.paramsList.filter((element) => element.key !== item.key);
      this.paramsList = filteredList;
    },
    editParamColor(item) {
      if (item.isNew) {
        return 'error';
      }
      if (item.value !== item.valueCopy) {
        return 'primary';
      }
      return 'secondary';
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
    newParamAdd() {
      if (this.newParamKey !== undefined && this.newParamKey !== null && this.newParamKey !== '') {
        const addValue = {
          key: '',
          value: '',
          valueCopy: '',
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
    makeRedirectUrl() {
      let baseUrl = this.editUrlTotal;
      if (this.paramsList.length === 0) {
        return baseUrl;
      }
      baseUrl = `${baseUrl}?`;
      let index = 0;
      this.paramsList.forEach((element) => {
        baseUrl += `${element.key}=${element.value}`;

        if (index + 1 < this.paramsList.length) {
          baseUrl += '&';
        }
        index += 1;
      });
      return baseUrl;
    },
    copyTextToClipBoard(copyText) {
      if (copyText !== undefined && copyText !== '') {
        if (navigator.clipboard) {
              navigator.clipboard.writeText(copyText).then(() => {
              });
          }
      }
    },
  },
  data: () => ({
    editPageInfo: {
      protocol: '',
      hostname: '',
      pathname: '',
      port: '',
    },
    editUrlTotal: '',
    editParamTotal: '',
    newParamKey: '',
    newParamValue: '',
    newParamResult: {
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
