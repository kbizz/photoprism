<template>
  <div class="p-page p-page-library">
    <v-tabs
        v-model="active"
        flat
        grow
        color="secondary"
        slider-color="secondary-dark"
        :height="$vuetify.breakpoint.smAndDown ? 48 : 64"
    >
      <v-tab v-for="(item, index) in tabs" :id="'tab-' + item.name" :key="index" :class="item.class" ripple
             @click="changePath(item.path)">
        <v-icon v-if="$vuetify.breakpoint.smAndDown" :title="item.label">{{ item.icon }}</v-icon>
        <template v-else>
          <v-icon :size="18" left>{{ item.icon }}</v-icon> {{ item.label }}
        </template>
      </v-tab>

      <v-tabs-items touchless>
        <v-tab-item v-for="(item, index) in tabs" :key="index" lazy>
          <component :is="item.component"></component>
        </v-tab-item>
      </v-tabs-items>
    </v-tabs>
  </div>
</template>

<script>
import Import from "pages/library/import.vue";
import Index from "pages/library/index.vue";
import Logs from "pages/library/logs.vue";

function initTabs(flag, tabs) {
  let i = 0;
  while (i < tabs.length) {
    if (!tabs[i][flag]) {
      tabs.splice(i, 1);
    } else {
      i++;
    }
  }
}

export default {
  name: 'PPageLibrary',
  props: {
    tab: String,
  },
  data() {
    const config = this.$config.values;
    const isDemo = this.$config.get("demo");
    const isPublic = this.$config.get("public");
    const isReadOnly = this.$config.get("readonly");
    const canImport = this.$config.feature('import') && !isReadOnly;

    const tabs = [
      {
        'name': 'library-index',
        'component': Index,
        'label': this.$gettext('Index'),
        'class': '',
        'path': '/library',
        'icon': 'camera_roll',
        'readonly': true,
        'demo': true,
      },
      {
        'name': 'library-import',
        'component': Import,
        'label': this.$gettext('Import'),
        'class': '',
        'path': '/library/import',
        'icon': 'perm_media',
        'readonly': false,
        'demo': true,
      },
      {
        'name': 'library-logs',
        'component': Logs,
        'label': this.$gettext('Logs'),
        'class': '',
        'path': '/library/logs',
        'icon': 'notes',
        'readonly': true,
        'demo': true,
      },
    ];

    if (isDemo) {
      initTabs("demo", tabs);
    }

    if (!canImport) {
      initTabs("readonly", tabs);
    }

    let active = 0;

    if (typeof this.tab === 'string' && this.tab !== '') {
      active = tabs.findIndex((t) => t.name === this.tab);
    }

    return {
      tabs: tabs,
      demo: isDemo,
      public: isPublic,
      config: config,
      readonly: isReadOnly,
      active: active,
    };
  },
  methods: {
    changePath: function (path) {
      if (this.$route.path !== path) {
        this.$router.replace(path);
      }
    }
  }
};
</script>
