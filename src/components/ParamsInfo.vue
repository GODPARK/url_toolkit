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
          <v-btn small @click="redirectAction()"> GO </v-btn>
        </v-toolbar>
        <v-card
          v-if="editBool"
          class="ma-4"
          elevation="0"
          outlined
          color="grey lighten-3"
        >
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
        >
          <template v-slot:[`item.key`]="{ item }">
            <v-chip
              color="secondary"
              small
              dark
            >
              {{ item.key }}
            </v-chip>
          </template>
          <template v-slot:[`item.value`]="{ item }">
            <div v-if="!editBool" style="width:85%"> {{ item.value }}</div>
            <v-text-field
              v-if="editBool"
              v-model="item.value"
              label="value"
              dense
              outlined
              single-line
              hide-details="true"
              :hint="item.value"
              style="width:85%"
            ></v-text-field>
          </template>
          <template v-slot:[`item.control`]="{ item }">
            <v-btn x-small text @click="copyTextToClipBoard(item)">
              <v-icon>
                mdi-content-paste
              </v-icon>
            </v-btn>
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
export default {
  name: 'ParamsInfo',
  components: {
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

    copyTextToClipBoard(item) {
      if (navigator.clipboard) {
            navigator.clipboard.writeText(item.value).then(() => {
                this.clipBoardResult.text = `'${item.key}' value is copy success!`;
                this.clipBoardResult.style.color = 'green';
            });
        } else {
            this.clipBoardResult.text = `${item.key} value is copy fail!`;
            this.clipBoardResult.style.color = 'red';
        }

        setTimeout(() => {
            this.clipBoardResult.text = '';
            this.clipBoardResult.style.color = 'grey';
        }, 2000);
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
        };
        addValue.key = this.newParamKey;
        if (this.newParamValue !== undefined && this.newParamValue !== null) {
          addValue.value = this.newParamValue;
        }
        this.paramsList.push(addValue);
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
