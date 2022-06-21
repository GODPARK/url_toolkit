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
                    URL Encode/Decode (UTF-8/ASCII)
                </v-toolbar-title>
                <v-spacer />
                <v-btn text @click="uiControl()">
                    <v-icon>
                        {{ uiControlbuttonText }}
                    </v-icon>
                </v-btn>
            </v-toolbar>
            <v-container v-if="uiOpenStatusBool">
                <v-row>
                    <v-col cols="12">
                        <v-text-field
                            v-model="utf8Text"
                            label="UTF-8"
                            outlined
                            hide-details="true"
                            dense
                            clearable
                            prepend-icon="mdi-content-paste"
                            append-outer-icon="mdi-transfer"
                            @click:append-outer="encodeUtf8ToAscii()"
                            @click:prepend="copyTextToClipBoard('utf8', utf8Text)"
                            :success="utf8SuccessBool"
                            :error="utf8ErrorBool"
                        ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                        <v-text-field
                            v-model="asciiText"
                            label="ASCII"
                            outlined
                            hide-details="true"
                            dense
                            clearable
                            prepend-icon="mdi-content-paste"
                            append-outer-icon="mdi-transfer"
                            @click:append-outer="decodeAsciiToUtf8()"
                            @click:prepend="copyTextToClipBoard('ascii', asciiText)"
                            :success="asciiSuccessBool"
                            :error="asciiErrorBool"
                        ></v-text-field>
                    </v-col>
                </v-row>
            </v-container>
        </v-card>
    </div>
</template>

<script>
export default {
  name: 'UrlEncode',
  components: {
  },
  props: [],
  computed: {
    uiControlbuttonText() {
        if (this.uiOpenStatusBool) {
            return 'mdi-chevron-up';
        }
        return 'mdi-chevron-down';
    },
  },
  mounted() {
  },
  methods: {
    uiControl() {
        this.uiOpenStatusBool = !this.uiOpenStatusBool;
    },
    encodeUtf8ToAscii() {
        if (this.asciiText !== '') {
            this.asciiText = '';
        }
        if (this.utf8Text !== '') {
            this.asciiText = encodeURIComponent(this.utf8Text);
        }
    },
    decodeAsciiToUtf8() {
        if (this.utf8Text !== '') {
            this.utf8Text = '';
        }
        if (this.asciiText !== '') {
            this.utf8Text = decodeURIComponent(this.asciiText);
        }
    },
    copyTextToClipBoard(type, text) {
        if (navigator.clipboard) {
            navigator.clipboard.writeText(text).then(() => {
                if (type === 'utf8') {
                    this.utf8SuccessBool = true;
                } else if (type === 'ascii') {
                    this.asciiSuccessBool = true;
                }
            });
        /* eslint-disable */
        } else {
            if (type === 'utf8') {
                this.utf8ErrorBool = true;
            } else if (type === 'ascii') {
                this.asciiErrorBool = true;
            }
        }

        setTimeout(() => {
            this.utf8SuccessBool = false;
            this.asciiSuccessBool = false;
            this.utf8ErrorBool = false;
            this.asciiErrorBool = false;
        }, 1500);
    },
  },
  data: () => ({
    utf8Text: '',
    asciiText: '',
    utf8SuccessBool: false,
    utf8ErrorBool: false,
    asciiSuccessBool: false,
    asciiErrorBool: false,
    uiOpenStatusBool: false,
  }),
};
</script>
