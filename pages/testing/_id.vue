<template>
  <section class="container">
    <div v-if="tweets.length">
      <div>
        <label><input type="checkbox" v-model="includeRetweet"> リツイートを含む</label>
        <label><input type="checkbox" v-model="includeReply"> リプライを含む</label>
      </div>
      <table class="table is-striped">
        <thead>
          <tr>
            <th>ユーザー</th>
            <th>Follower</th>
            <th>ツイート</th>
            <th>RT</th>
            <th>Fav</th>
            <th>Score</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="tweet in dispTweets" :key="tweet.id_str">
            <td><img :src="tweet.user.profile_image_url_https" width="48" height="48"> @{{ tweet.user.screen_name }}</td>
            <td class="has-text-right">{{ tweet.user.followers_count }}</td>
            <td>{{ tweet.text }}</td>
            <td class="has-text-right">{{ tweet.retweet_count }}</td>
            <td class="has-text-right">{{ tweet.favorite_count }}</td>
            <td class="has-text-right">{{ tweet.score }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <p v-else>ツイートの読み込み中</p>
  </section>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      tweets: [],
      includeRetweet: false,
      includeReply: false
    }
  },
  computed: {
    dispTweets() {
      return this.tweets
        .filter(tweet => {
          if (!this.includeRetweet && tweet.is_retweet) return false
          if (!this.includeReply && tweet.is_reply) return false
          return true
        })
        .map(tweet => {
          tweet.score = Math.floor((tweet.retweet_count * 1 + tweet.favorite_count * 3) / tweet.user.followers_count * 100000)
          return tweet
        })
        .sort((a, b) => {
          if (a.score < b.score) return 1;
          if (a.score > b.score) return -1;
          return 0;
        })
    }
  },
  mounted() {
    this.getData()
  },
  methods: {
    async getData() {
      this.tweets = (await axios.get('http://localhost:8080/testing/1CsOlflj6bajEEBrUaVr')).data.tweets
    }
  }
}
</script>
