<template>
  <div class="container">
    <img src="../assets/image.svg" alt="" />
    <div v-if="!connected" class="row mt-4">
      <div class="col-3 mx-auto">
        <div v-if="wrongApi" class="card bg-danger mb-4">
          <div class="card-body">
            <p class="card-text">Wrong API Key</p>
          </div>
        </div>
        <div v-if="internalError" class="card bg-danger mb-4">
          <div class="card-body">
            <p class="card-text">Internal Server Error</p>
          </div>
        </div>
        <div class="card">
          <div class="card-body">
            <p class="card-text">API Key</p>

            <input class="apiInput" v-model="apiKey" type="text" placeholder="Bitpanda API Key" />
            <div class="mt-4">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#27d17f" class="bi bi-square"
                viewBox="0 0 16 16" v-if="!remember" @click="remember = true">
                <path
                  d="M14 1a1 1 0 0 1 1 1v12a1 1 0 0 1-1 1H2a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h12zM2 0a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2H2z" />
              </svg>
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="#27d17f" class="bi bi-check-square"
                viewBox="0 0 16 16" v-else @click="remember = false">
                <path
                  d="M14 1a1 1 0 0 1 1 1v12a1 1 0 0 1-1 1H2a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h12zM2 0a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2H2z" />
                <path
                  d="M10.97 4.97a.75.75 0 0 1 1.071 1.05l-3.992 4.99a.75.75 0 0 1-1.08.02L4.324 8.384a.75.75 0 1 1 1.06-1.06l2.094 2.093 3.473-4.425a.235.235 0 0 1 .02-.022z" />
              </svg>
              <p class="d-inline m-2" style="color: #27d17f">Remember</p>
            </div>
            <div class="btn mt-4">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                class="bi bi-arrow-right-square-fill" viewBox="0 0 16 16">
                <path
                  d="M0 14a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2H2a2 2 0 0 0-2 2v12zm4.5-6.5h5.793L8.146 5.354a.5.5 0 1 1 .708-.708l3 3a.5.5 0 0 1 0 .708l-3 3a.5.5 0 0 1-.708-.708L10.293 8.5H4.5a.5.5 0 0 1 0-1z" />
              </svg>
              <div class="btn-text m-2 d-inline" @click="connect">Connect</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <div v-if="cryptoindexes.length > 0">
        <p class="card-text fs-1">Cryptoindexes</p>
        <div class="row mt-4">
          <div v-for="crypto in cryptoindexes" :key="crypto.attributes.cryptocoin_id" class="col-2 mt-4">
            <div class="card">
              <div class="card-body">
                <p class="avatar m-2">
                  {{ crypto.attributes.cryptocoin_symbol }}
                </p>
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ crypto.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{ tickers[crypto.attributes.cryptocoin_symbol]["USD"] }}$
                  </p>
                </div>
                <p class="card-text m-2 fs-4">
                  {{ parseFloat(crypto.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="etfs.length > 0">
        <p class="card-text fs-1">ETF</p>
        <div class="row mt-4">
          <div v-for="etf in etfs" :key="etf.attributes.cryptocoin_id" class="col-2 mt-4">
            <div class="card">
              <div class="card-body">
                <p class="avatar m-2">
                  {{ etf.attributes.cryptocoin_symbol }}
                </p>
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ etf.attributes.cryptocoin_symbol }}
                  </p>
                  <!--
              <p class="card-text d-table-row">
                {{ tickers[crypto.attributes.cryptocoin_symbol]["USD"] }}$
              </p>
              -->
                </div>
                <p class="card-text m-2 fs-4">
                  {{ parseFloat(etf.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="stocks.length > 0">
        <p class="card-text fs-1">Stocks</p>
        <div class="row mt-4">
          <div v-for="stock in stocks" :key="stock.attributes.cryptocoin_id" class="col-2 mt-4">
            <div class="card">
              <div class="card-body">
                <p class="avatar m-2">
                  {{ stock.attributes.cryptocoin_symbol }}
                </p>
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ stock.attributes.cryptocoin_symbol }}
                  </p>
                </div>
                <p class="card-text m-2 fs-4">
                  {{ parseFloat(stock.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="commodities.length > 0">
        <p class="card-text fs-1">Commodity</p>
        <div class="row mt-4">
          <div v-for="commodity in commodities" :key="commodity.attributes.cryptocoin_id" class="col-2 mt-4">
            <div class="card">
              <div class="card-body">
                <p class="avatar m-2">
                  {{ commodity.attributes.cryptocoin_symbol }}
                </p>
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ commodity.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{ tickers[commodity.attributes.cryptocoin_symbol]["USD"] }}$
                  </p>
                </div>
                <p class="card-text m-2 fs-4">
                  {{ parseFloat(commodity.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="cryptocoins.length > 0">
        <p class="card-text fs-1">Cryptocoins</p>
        <div class="row mt-4">
          <div v-for="cryptocoin in cryptocoins" :key="cryptocoin.attributes.cryptocoin_id" class="col-2 mt-4">
            <div class="card">
              <div class="card-body">
                <p class="avatar m-2">
                  {{ cryptocoin.attributes.cryptocoin_symbol }}
                </p>
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ cryptocoin.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{ tickers[cryptocoin.attributes.cryptocoin_symbol]["USD"] }}$
                  </p>
                </div>
                <p class="card-text m-2 fs-4">
                  {{ parseFloat(cryptocoin.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HomePage",
  data() {
    return {
      connected: false,
      remember: false,
      wrongApi: false,
      internalError: false,
      apiKey: null, //a921da3c24ba854a4d92874079268d975cebe2108a6625ebaaea8c072ff04e93ca877863973a289419444d8be81fc45175fbe8b8f708e78b55c80dfc9911b9ba
      cryptoindexes: null,
      etfs: null,
      stocks: null,
      commodities: null,
      cryptocoins: null,
      tickers: null,
    };
  },
  methods: {
    async connect() {
      this.wrongApi = false;
      this.internalError = false;
      let uri =
        "https://dashboard-cors.herokuapp.com/https://api.bitpanda.com/v1/asset-wallets";
      let config = {
        headers: {
          "Content-Type": "application/json",
          "X-Api-Key": this.apiKey,
        },
      };
      let res = await axios.get(uri, config).catch((error) => {
        if (error.response.status == 401) this.wrongApi = true;
        else if (error.response.status == 500) this.internalError = true;
      });
      //let cryptocoin = res.data.data.attributes.cryptocoin;
      if (res.status == 200) {
        this.connected = true;
        if (this.remember) localStorage.setItem("apiKey", this.apiKey);
        this.etfs = res.data.data.attributes.security.etf.attributes.wallets;
        this.cryptoindexes =
          res.data.data.attributes.index.index.attributes.wallets;
        this.stocks =
          res.data.data.attributes.security.stock.attributes.wallets;
        this.commodities =
          res.data.data.attributes.commodity.metal.attributes.wallets;
        this.cryptocoins =
          res.data.data.attributes.cryptocoin.attributes.wallets;
      }
      uri = "https://api.bitpanda.com/v1/ticker";
      res = await axios.get(uri);
      if (res.status == 200) {
        this.tickers = res.data;
      }
    },
  },
  mounted() {
    this.apiKey = localStorage.getItem("apiKey");
    if (this.apiKey !== null) this.connect();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.apiInput {
  outline: 0;
  border-width: 0 0 2px;
  border-color: #27d17f;
  color: #27d17f;
  background-color: transparent;
}
.checboxInput {
  outline: 1px solid #27d17f;
  color: transparent;
}
.card {
  background-color: #282828;
}
.card-text {
  color: white;
}
.btn {
  background-color: #27d17f;
}
.btn-text {
  color: black;
}
.avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  color: #fff;
  line-height: 48px;
  font-size: 12px;
  text-align: center;
  background-color: #27d17f;
  display: inline-block;
}
</style>
