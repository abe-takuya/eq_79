<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />

        <q-toolbar-title>
          EQ-79
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      content-class="bg-grey-1"
    >
      <q-list>
        <q-item to="/">
          <q-item-section avatar>
            <q-icon name="home" />
          </q-item-section>
          <q-item-section>
            <q-item-label>HOME</q-item-label>
          </q-item-section>
        </q-item>

        <q-separator spaced />

        <q-item>
          <q-item-section avatar>
            <q-icon name="category" />
          </q-item-section>
          <q-item-section>
            <q-item-label class="text-grey-8">
              Categories
            </q-item-label>
          </q-item-section>
        </q-item>

        <q-item
          v-for="category in categories"
          v-bind:key="category"
          :to="{name: 'joke', params: {category: category}}"
        >
          <q-item-section avatar></q-item-section>
          <q-item-section>
            <q-item-label>{{ category }}</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  components: {},
  data() {
    return {
      leftDrawerOpen: false,
      categories: [],
    };
  },
  mounted() {
    this.$axios.get('https://api.chucknorris.io/jokes/categories')
      .then((response) => response.data)
      .then((data) => {
        this.categories = data;
      })
      .finally(() => {
        this.loading = false;
      });
  },
};
</script>
