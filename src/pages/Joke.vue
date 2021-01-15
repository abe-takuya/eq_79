<template>
  <q-page padding>
    <div class="q-pa-md items-start q-gutter-md">
      <div class="row">
        <div class="col">
          <h3 class="q-my-xs">{{ this.category }}</h3>
        </div>
      </div>
      <div v-if="this.joke">
        <div class="row">
          <div class="col q-pa-md q-gutter-sm">
            <p>{{ this.joke.value }}</p>
            <q-btn @click="reload" label="Another Fact" />
            <q-btn @click="openUrl" label="Website" />
          </div>
        </div>
        <div class="row">
          <div class="col">
            <h5 class="q-mb-xs">Related Categories</h5>
            <ul>
              <li v-for="category in this.joke.categories" v-bind:key="category">
                <router-link
                  :to="{name: 'joke', params: {category: category}}">{{ category }}</router-link>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div v-else>
        <q-spinner
          color="primary"
          size="3em"
        />
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageJoke',
  props: ['category'],
  data() {
    return {
      joke: null,
      loading: false,
      response: null,
    };
  },
  beforeRouteUpdate(to, from, next) {
    this.load(to.params.category);
    next();
  },
  mounted() {
    this.load(this.category);
  },
  computed: {
    categories() {
      return this.joke.categories.join(',');
    },
  },
  methods: {
    openUrl() {
      window.open(this.joke.url);
    },
    load(category) {
      this.joke = null;
      this.$axios.get(`https://api.chucknorris.io/jokes/random?category=${category}`)
        .then((response) => response.data)
        .then((data) => {
          this.joke = data;
        })
        .finally(() => {
        });
    },
    reload() {
      this.load(this.category);
    },
  },
};
</script>
