<template>
<div id="appComponent">
  <h1 style="color:rgb(220, 220, 220);">Coin Markets</h1>
  <div class="coinRow">
    <strong>Coin</strong>
    <strong>Name</strong>
    <strong>Market Cap $</strong>
    <strong>Action</strong>
  </div>
  <div v-for="(coin, index) in coins" :key="`coin-${index}`" class="coinRow">
    <img :src="coin.image" :alt="coin.name" height="30px" width="auto" style="">
    <div>{{coin.name}}</div>
    <span>{{coin.market_cap.toLocaleString()}}</span>
    <button class="btn" @click="open(coin.id)">Detail</button>
  </div>
  <vue-bottom-sheet ref="myBottomSheet"
    :swipe-able="true"
    :click-to-close="true"
    :is-full-screen="true"
    max-width="390px"
    max-height="90%"
    >
    <div class="moreInfo">
      <div class="heading">
        <a :href="coinDetail.links.homepage[0]" v-if="coinDetail.links.homepage" target="_blank"><img :src="coinDetail.image.small" :alt="coinDetail.name" height="40px" width="auto"></a>
        <img :src="coinDetail.image.small" :alt="coinDetail.name" height="40px" width="auto" v-else>

        <h1>{{coinDetail.name}}</h1>
      </div>
      <p class="description" v-if="coinDetail.description.en" v-html="coinDetail.description.en"></p>
      <div class="urls" v-if="coinDetail.links.subreddit_url">
        Join the discussion <span><a :href="coinDetail.links.subreddit_url" target="_blank"><img src="https://toppng.com/uploads/preview/reddit-logo-reddit-icon-115628658968pe8utyxjt.png" alt="Reddit" height="30px" width="auto"></a></span>
      </div>
    </div>
  </vue-bottom-sheet>
  </div>
</template>


<script>
import  VueBottomSheet from "@webzlodimir/vue-bottom-sheet";

export default {
  components: {
    VueBottomSheet
  },
  data() {
    return {
      coins: [],
      coinDetail: {
        name: "",
        image: {},
        description: {},
        links: {
          homepage: [],
          subreddit_url: "",
        },
      },
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
      fetch("https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false")
        .then(response => response.json())
        .then(json => {
          this.coins = json.splice(0, 30);
        });
    },
    async open(coinId) {
      await fetch(`https://api.coingecko.com/api/v3/coins/${coinId}`)
        .then(response => response.json())
        .then(json => {
            this.coinDetail = json;
            this.$refs.myBottomSheet.open();
        });
    },
    close() {
      this.coinDetail = [];
      this.$refs.myBottomSheet.close();
    }
  }
}
</script>

<style scoped>
#appComponent {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin:0 auto;
  padding: 20px 25px;
  max-width: 400px;
  background-color: #2c3e50;
}
.coinRow {
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 30px 0;
  color: rgb(220, 220, 220);
  border-bottom: 1px solid rgb(169, 169, 169);
}
.btn {
  background-color: aliceblue;
  color: black;
  border: unset;
  padding: 10px;
  border-radius: 2px;
  cursor: pointer;
}
.moreInfo {
  padding: 20px;
}
.description{
  text-align: justify;
  margin-bottom: 10px;
}
.urls {
  text-align: left;
}
</style>
