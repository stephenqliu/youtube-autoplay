<template>
  <div id="app" v-if="dataLoaded">
    <div class="section">
      <div class="option-wrap">
        <div class="option">
          <v-form-field input-id="ap" :label="getText('optionTitle_autoplay')">
            <v-switch id="ap" v-model="options.autoplay"></v-switch>
          </v-form-field>
        </div>
        <div class="option">
          <v-form-field
            input-id="app"
            :label="getText('optionTitle_autoplayPlaylist')"
          >
            <v-switch id="app" v-model="options.autoplayPlaylist"></v-switch>
          </v-form-field>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {FormField, Switch} from 'ext-components';

import storage from 'storage/storage';
import {getText} from 'utils/common';
import {optionKeys} from 'utils/data';

export default {
  components: {
    [FormField.name]: FormField,
    [Switch.name]: Switch
  },

  data: function() {
    return {
      dataLoaded: false,

      options: {
        autoplay: false,
        autoplayPlaylist: false
      }
    };
  },

  methods: {
    getText
  },

  created: async function() {
    const options = await storage.get(optionKeys);

    for (const option of Object.keys(this.options)) {
      this.options[option] = options[option];
      this.$watch(`options.${option}`, async function(value) {
        await storage.set({[option]: value});
      });
    }

    document.title = getText('pageTitle', [
      getText('pageTitle_options'),
      getText('extensionName')
    ]);

    this.dataLoaded = true;
  }
};
</script>

<style lang="scss">
$mdc-theme-primary: #1abc9c;

@import '@material/typography/mixins';

body {
  margin: 0;
  @include mdc-typography-base;
  font-size: 100%;
  background-color: #ffffff;
  overflow: visible !important;
}

#app {
  display: grid;
  grid-row-gap: 32px;
  padding: 24px;
}

.mdc-switch {
  margin-right: 16px;
}

.option-wrap {
  display: grid;
  grid-row-gap: 24px;
  grid-auto-columns: min-content;
}

.option {
  display: flex;
  align-items: center;
  height: 24px;

  & .mdc-form-field {
    max-width: calc(100vw - 48px);

    & label {
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
  }
}
</style>
