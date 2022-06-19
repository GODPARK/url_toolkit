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
                <v-card
                    class="mr-1 ml-1"
                    elevation="0"
                    color="grey lighten-2"
                >
                    <div class="mr-2 ml-2 pa-2" style="font-size:0.7rem">
                        {{ pageInfo.href }}
                    </div>
                    <v-card-actions>
                        <v-spacer />
                        <div :style="copyResult.style"> {{copyResult.text}} </div>
                        <v-btn
                            @click="copyUrlToClipBoard(pageInfo.href)"
                            x-small text style="color:#616161;"
                        >
                            <v-icon style="font-size:1rem;">mdi-content-paste</v-icon>
                        </v-btn>
                    </v-card-actions>
                </v-card>
                <v-list dense>
                    <v-list-item>
                        <v-list-item-content>Path</v-list-item-content>
                        <v-list-item-content class="align-end font-weight-bold">
                            {{ pageInfo.pathname }}
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item>
                        <v-list-item-content>Port</v-list-item-content>
                        <v-list-item-content class="align-end font-weight-bold">
                            {{ portParser(pageInfo.protocol, pageInfo.port) }}
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
    pageInfo: {
        default: () => new URL(''),
    },
  },
  computed: {},
  mounted() {
  },
  methods: {
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
    portParser(protocol, port) {
        if (port !== '') {
            return port;
        }
        switch (protocol) {
            case 'https:':
                return '443';
            case 'http:':
                return '80';
            case 'ldap:':
                return '389';
            case 'ldaps:':
                return '636';
            default:
                return '';
        }
    },
    copyUrlToClipBoard(url) {
        if (navigator.clipboard) {
            navigator.clipboard.writeText(url).then(() => {
                this.copyResult.text = 'copy success!';
                this.copyResult.style.color = 'green';
            });
        } else {
            this.copyResult.text = 'copy fail';
            this.copyResult.style.color = 'red';
        }

        setTimeout(() => {
            this.copyResult.text = '';
            this.copyResult.style.color = 'gray';
        }, 1500);
    },
  },
  data: () => ({
    copyResult: {
        text: '',
        style: {
            color: 'gray',
            'font-size': '0.5rem',
        },
    },
  }),
};
</script>
