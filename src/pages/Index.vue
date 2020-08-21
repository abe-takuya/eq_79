<template>
  <q-page padding>
    <div class="q-pa-md row justify-center">
      <q-page-sticky expand position="bottom">
        <q-toolbar class="bg-primary text-white rounded-borders">
          <form @submit.prevent="submit">
            <q-input dark dense standout v-model="query" class="q-ml-md full-width" maxlength="120">
              <template v-slot:append>
                <q-icon v-if="query === ''" name="comment" />
                <q-icon v-else name="clear" class="cursor-pointer" @click="query = ''" />
              </template>
            </q-input>
          </form>
        </q-toolbar>
      </q-page-sticky>
      <div style="width: 100%;">
      <q-chat-message
        avatar="icons/icon-128x128.png"
        :text="['チャックノリスの真実をランダムにお送りします。']"
      />

      <q-chat-message
        v-for="(joke, index) in jokes"
        :key="index"
        :avatar="joke.icon_url"
        :text="[joke.value]"
        :sent="!joke.icon_url"
      />

      <q-chat-message
        v-if="loading"
        avatar="icons/icon-128x128.png"
        bg-color="amber"
      >
        <q-spinner-dots size="2rem" />
      </q-chat-message>
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      jokes: [],
      query: '',
      loading: false,
      response: null,
    };
  },
  mounted() {
    this.loading = true;
    this.$axios.get('https://api.chucknorris.io/jokes/random')
      .then((response) => response.data)
      .then((data) => {
        this.jokes.push(data);
      })
      .finally(() => {
        this.loading = false;
      });
  },
  methods: {
    submit() {
      if (this.query === '') {
        this.jokes.push({
          value: ['真実を探したいワードを入力してください。'],
          icon_url: 'icons/icon-128x128.png',
        });
        return;
      }

      if (this.query.length < 3) {
        this.jokes.push({
          value: ['ワードは3文字以上入力してください。'],
          icon_url: 'icons/icon-128x128.png',
        });
        return;
      }

      this.jokes.push({
        value: [this.query],
        icon_url: null,
      });

      this.loading = true;
      this.$axios.get(`https://api.chucknorris.io/jokes/search?query=${this.query}`)
        .then((response) => response.data)
        .then((data) => {
          if (data.total === 0) {
            return [{
              value: ['真実が見つかりませんでした。違うワードで試してみてください。'],
              icon_url: 'icons/icon-128x128.png',
            }];
          }
          this.jokes.push({
            value: `${data.total} 件の真実が見つかりました。`,
            icon_url: 'icons/icon-128x128.png',
          });

          return data.result.slice(0, 5);
        })
        .then((results) => {
          this.jokes = [...this.jokes, ...results];
        })
        .finally(() => {
          this.loading = false;
          this.query = '';
        });
    },
  },
};
</script>
