<template>
  <div class="container">
    <div class="text-center">
      <img src="../assets/imgs/image.png" alt="" />
    </div>
    <div v-if="connectionStatus === 'disconnected'" class="row mt-4 text-center">
      <div class="col-10 col-sm-8 col-md-6 col-lg-4 col-xl-3 mx-auto">
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
            <div class="btn mt-4 btn-connect">
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
    <div v-else-if="connectionStatus === 'connecting'">
      <loading v-model:active="isLoading" :can-cancel="false" :is-full-page="true" color="#27d17f"
        background-color="#000" loader="dots" />
    </div>
    <div v-else>
      <!-- connected -->
      <div class="text-end">
        <div class="btn btn-outline-danger btn-disconnect" @click="disconnect">
          Disconnect
        </div>
      </div>
      <div v-if="cryptoindexes !== null && cryptoindexes.length > 0">
        <p class="card-text fs-1 m-0">Cryptoindexes</p>
        <div class="row">
          <div v-for="crypto in cryptoindexes" :key="crypto.attributes.cryptocoin_id"
            class="col-sm-6 col-md-4 col-lg-3 col-xl-2 mt-4">
            <div class="card">
              <div class="card-body">
                <img :src="
                  'https://ui-avatars.com/api/?background=random&name=' +
                  crypto.attributes.cryptocoin_symbol +
                  '&rounded=true&length=10&font-size=0.25'
                " alt="" class="avatar" />
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ crypto.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{ tickers[crypto.attributes.cryptocoin_symbol]["USD"] }}$
                  </p>
                </div>
                <p class="card-text semi-bold m-2 fs-4">
                  {{ parseFloat(crypto.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="etfs !== null && etfs.length > 0">
        <p class="card-text fs-1 m-0 mt-4">ETF</p>
        <div class="row">
          <div v-for="etf in etfs" :key="etf.attributes.cryptocoin_id" class="col-sm-6 col-md-4 col-lg-3 col-xl-2 mt-4">
            <div class="card">
              <div class="card-body">
                <img :src="
                  'https://ui-avatars.com/api/?background=random&name=' +
                  etf.attributes.cryptocoin_symbol +
                  '&rounded=true&length=10&font-size=0.25'
                " alt="" class="avatar" />
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ etf.attributes.cryptocoin_symbol }}
                  </p>
                </div>
                <p class="card-text semi-bold m-2 fs-4">
                  {{ parseFloat(etf.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="stocks !== null && stocks.length > 0">
        <p class="card-text fs-1 m-0 mt-4">Stocks</p>
        <div class="row">
          <div v-for="stock in stocks" :key="stock.attributes.cryptocoin_id"
            class="col-sm-6 col-md-4 col-lg-3 col-xl-2 mt-4">
            <div class="card">
              <div class="card-body">
                <!-- & encoding not working-->
                <img :src="
                  'https://ui-avatars.com/api/?background=random&name=' +
                  stock.attributes.cryptocoin_symbol.replaceAll('&', '%26') +
                  '&rounded=true&length=10&font-size=0.25'
                " alt="" class="avatar" />
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ stock.attributes.cryptocoin_symbol }}
                  </p>
                </div>
                <p class="card-text semi-bold m-2 fs-4">
                  {{ parseFloat(stock.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="commodites !== null && commodities.length > 0">
        <p class="card-text fs-1 m-0 mt-4">Commodity</p>
        <div class="row">
          <div v-for="commodity in commodities" :key="commodity.attributes.cryptocoin_id"
            class="col-sm-6 col-md-4 col-lg-3 col-xl-2 mt-4">
            <div class="card">
              <div class="card-body">
                <img :src="
                  'https://ui-avatars.com/api/?background=random&name=' +
                  commodity.attributes.cryptocoin_symbol +
                  '&rounded=true&length=10&font-size=0.25'
                " alt="" class="avatar" />
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ commodity.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{
                    tickers[commodity.attributes.cryptocoin_symbol]["USD"]
                    }}$
                  </p>
                </div>
                <p class="card-text semi-bold m-2 fs-4">
                  {{ parseFloat(commodity.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="cryptocoins !== null && cryptocoins.length > 0">
        <p class="card-text fs-1 m-0 mt-4">Cryptocoins</p>
        <div class="row">
          <div v-for="cryptocoin in cryptocoins" :key="cryptocoin.attributes.cryptocoin_id"
            class="col-sm-6 col-md-4 col-lg-3 col-xl-2 mt-4">
            <div class="card">
              <div class="card-body">
                <img :src="
                  'https://raw.githubusercontent.com/rainner/crypto-icons/main/icons/' +
                  cryptocoin.attributes.cryptocoin_symbol.toLowerCase() +
                  '.png'
                " alt="" class="avatar" @error="
                  $event.target.src =
                    'https://ui-avatars.com/api/?background=random&name=' +
                    cryptocoin.attributes.cryptocoin_symbol +
                    '&rounded=true&length=10&font-size=0.25'
                " />
                <div class="d-inline m-2">
                  <p class="card-text d-table-row">
                    {{ cryptocoin.attributes.cryptocoin_symbol }}
                  </p>
                  <p class="card-text d-table-row">
                    {{
                    tickers[cryptocoin.attributes.cryptocoin_symbol]["USD"]
                    }}$
                  </p>
                </div>
                <p class="card-text semi-bold m-2 fs-4">
                  {{ parseFloat(cryptocoin.attributes.balance).toFixed(2) }}$
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-if="transactions !== null && transactions.length > 0">
        <p class="card-text fs-1 m-0 mt-4">Trades</p>
        <table class="table card-text">
          <thead class="semi-bold">
            <tr>
              <th scope="col">Trade</th>
              <th scope="col">Amount</th>
              <th class="d-none d-sm-table-cell" scope="col">USD Amount</th>
              <th class="d-none d-sm-table-cell" scope="col">Swap Price</th>
              <th class="d-none d-lg-table-cell" scope="col">Fee</th>
              <th class="d-none d-md-table-cell" scope="col">Time</th>
              <th scope="col">Type</th>
            </tr>
          </thead>
          <tbody>
            <tr scope="row" v-for="transaction in transactions" :key="transaction.attributes.trade_id">
              <td>
                <span>
                  <img :src="
                    'https://raw.githubusercontent.com/rainner/crypto-icons/main/icons/' +
                    transaction.attributes.cryptocoin_symbol.toLowerCase() +
                    '.png'
                  " alt="" class="avatar-mini d-none d-md-inline" @error="
                    $event.target.src =
                      'https://ui-avatars.com/api/?background=random&name=' +
                      transaction.attributes.cryptocoin_symbol +
                      '&rounded=true&length=10&font-size=0.25'
                  " />
                  <img v-if="transaction.attributes.trade" :src="
                    'https://raw.githubusercontent.com/rainner/crypto-icons/main/icons/' +
                    getTickerFromId(transaction.attributes.trade.attributes.related_swap_trade.attributes.cryptocoin_id).toLowerCase() +
                    '.png'
                  " alt="" class="avatar-mini d-none d-md-inline" @error="
                    $event.target.src =
                      'https://ui-avatars.com/api/?background=random&name=' +
                      getTickerFromId(transaction.attributes.trade.attributes.related_swap_trade.attributes.cryptocoin_id) +
                      '&rounded=true&length=10&font-size=0.25'
                  " />
                  {{ transaction.attributes.cryptocoin_symbol }}
                </span>
                <span class="pl-0" v-if="transaction.attributes.trade">
                  / {{
                  getTickerFromId(transaction.attributes.trade.attributes.related_swap_trade.attributes.cryptocoin_id)
                  }}
                </span>
              </td>
              <td>{{ parseFloat(transaction.attributes.amount).toFixed(2) }}</td>
              <td class="d-none d-sm-table-cell">
                <div v-if="transaction.attributes.trade">
                  {{
                  (
                  parseFloat(transaction.attributes.amount) *
                  parseFloat(
                  transaction.attributes.trade.attributes.price
                  )
                  ).toFixed(2)
                  }}$
                </div>
              </td>
              <td class="d-none d-sm-table-cell">
                <div v-if="transaction.attributes.trade">
                  {{parseFloat(transaction.attributes.trade.attributes.price).toFixed(2) }}$
                </div>
              </td>
              <td class="d-none d-lg-table-cell">{{ parseFloat(transaction.attributes.fee).toFixed(2) }}$</td>
              <td class="d-none d-md-table-cell">
                {{
                new Date(
                transaction.attributes.time.unix * 1000
                ).toLocaleTimeString("en-US")
                }}
              </td>
              <td>
                {{ transaction.attributes.type.toUpperCase() }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";

export default {
  name: "HomePage",
  components: {
    Loading,
  },
  data() {
    return {
      connectionStatus: "disconnected",
      isLoading: false,
      remember: false,
      wrongApi: false,
      internalError: false,
      apiKey: null,
      cryptoindexes: null,
      etfs: null,
      stocks: null,
      commodities: null,
      cryptocoins: null,
      tickers: null,
      transactions: null,
    };
  },
  methods: {
    async connect() {
      let uri = "https://api.bitpanda.com/v1/ticker";
      let res = await axios.get(uri).catch((error) => {
        this.isLoading = false;
        this.connectionStatus = "disconnected";
        if (error.response.status === 500) this.internalError = true;
        return;
      });
      if (res.status == 200) {
        this.tickers = res.data;
      }

      this.connectionStatus = "connecting";
      this.isLoading = true;
      this.wrongApi = false;
      this.internalError = false;
      uri = 'https://corsproxy.io/?' + encodeURIComponent('https://api.bitpanda.com/v1/asset-wallets');
      let config = {
        headers: {
          "Content-Type": "application/json",
          "X-Api-Key": this.apiKey,
        },
      };
      res = await axios.get(uri, config).catch((error) => {
        this.isLoading = false;
        this.connectionStatus = "disconnected";

        if (error.response.status === 401) this.wrongApi = true;
        else if (error.response.status === 500) this.internalError = true;
        return;
      });
      if (res.status == 200) {
        this.isLoading = false;
        this.connectionStatus = "connected";
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

      uri = 'https://corsproxy.io/?' + encodeURIComponent('https://api.bitpanda.com/v1/wallets/transactions?page_size=100');
      res = await axios.get(uri, config).catch((error) => {
        if (error.response.status === 500) this.internalError = true;
        return;
      });
      if (res.status == 200) {
        this.transactions = res.data.data;
        //console.log(this.transactions);
      }
    },
    disconnect() {
      this.isLoading = true;
      localStorage.removeItem("apiKey");
      //this.apiKey = null;
      this.connectionStatus = "disconnected";
      this.isLoading = false;
    },
    getTickerFromId(id) {
      for (const item of [this.cryptoindexes, this.etfs, this.stocks, this.commodities, this.cryptocoins]) {
        let found = item.find((elem) => elem.attributes.cryptocoin_id === id);
        if (found !== undefined) return (found.attributes.cryptocoin_symbol);
      }
      return "NF";
    }
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
.btn-connect {
  background-color: #27d17f;
}
.btn-text {
  color: black;
}
.avatar {
  width: 48px;
  height: 48px;
  display: inline-block;
}
.avatar-mini {
  width: 24px;
  height: 24px;
  display: inline-block;
}
.semi-bold {
  font-family: BR-Candor-SemiBold;
}
thead {
  color: #27d17f;
}
tr:hover {
  color: #27d17f;
}
</style>
