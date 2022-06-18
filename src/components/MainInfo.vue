<template>
    <div>
        <v-card
            elevation="4"
            outlined
        >
            <v-card-title>
                <v-chip
                    class="ma-2"
                    color="primary"
                    small
                >
                    {{ protocolParser(pageInfo.protocol) }}
                </v-chip>
                {{ pageInfo.hostname }}
            </v-card-title>
            <v-card-text>
                <v-list dense>
                    <v-list-item>
                        <v-list-item-content>Path:</v-list-item-content>
                        <v-list-item-content class="align-end font-weight-bold">
                            {{ pageInfo.pathname }}
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-content>Port:</v-list-item-content>
                        <v-list-item-content class="align-end font-weight-bold">
                            {{ pageInfo.port }}
                        </v-list-item-content>
                    </v-list-item>
                </v-list>
            </v-card-text>
        </v-card>
    </div>
</template>

<script>

export default {
  name: 'MainInfo',
  components: {
  },
  props: {
    testBool: {
        type: Boolean,
        default: () => false,
    },
    testData: {
        type: Object,
        default: () => {},
    },
  },
  computed: {},
  mounted() {
    this.makePage();
  },
  methods: {
    async getCurrentUrl() {
        const queryOptions = { active: true, currentWindow: true };
        if (!this.testBool) {
            /* eslint-disable */
            const tabs = await chrome.tabs.query(queryOptions);
            if (tabs.length > 0) {
                if (tabs[0].url !== undefined && tabs[0].url !== '') {
                    return tabs[0].url;
                }
            }
        }
        return '';
    },

    async makePage() {
        const url = await this.getCurrentUrl();
        if (this.testBool) {
            this.pageInfo = new URL(this.testData.url);
        } else {
            this.pageInfo = new URL(url);
        }
        /* eslint-disable */
        console.log(this.pageInfo);
    },

    protocolParser(protocol) {
        let protocolText = protocol;
        if (protocol === 'https:') {
            protocolText = 'https';
        } else if (protocol === 'http:') {
            protocolText = 'http';
        } else if (protocol === '') {
            protocolText = 'unknown';
        }
        return protocolText;
    },
  },
  data: () => ({
    pageInfo: {},
  }),
};
</script>
