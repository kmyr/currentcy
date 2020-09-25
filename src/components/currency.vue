<template>
  <div class="currency-section">
    <div class="container" id="currencyPriceSection">
      <div class="d-flex" id="currencyPricePart">
        <p class="text-muted">SELL</p>
        <h3 class="bold-text" id="currencyPriceSell">
          <span style="color:green">
            <svg
              width="1em"
              height="1em"
              viewBox="0 0 16 16"
              class="bi bi-plus"
              fill="currentColor"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M8 3.5a.5.5 0 0 1 .5.5v4a.5.5 0 0 1-.5.5H4a.5.5 0 0 1 0-1h3.5V4a.5.5 0 0 1 .5-.5z"
              />
              <path
                fill-rule="evenodd"
                d="M7.5 8a.5.5 0 0 1 .5-.5h4a.5.5 0 0 1 0 1H8.5V12a.5.5 0 0 1-1 0V8z"
              />
            </svg>
          </span>
          {{ showingCurrency.sell }}
        </h3>
      </div>
      <div class="d-flex" id="currencyPricePart">
        <p class="text-muted">BUY</p>
        <h3 class="bold-text" id="currencyPriceBuy">
          <span style="color:red">
            <svg
              width="1em"
              height="1em"
              viewBox="0 0 16 16"
              class="bi bi-dash"
              fill="currentColor"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M3.5 8a.5.5 0 0 1 .5-.5h8a.5.5 0 0 1 0 1H4a.5.5 0 0 1-.5-.5z"
              />
            </svg>
          </span>
          {{ showingCurrency.buy }}
        </h3>
      </div>
      <div class="d-flex" id="currencySelectSection">
        <img
          class="svg-img"
          id="contriesFlagIcon"
          :src="showingCurrency.flag"
        />
        <select
          class="trasnparent-dropdown"
          id="currencySelect"
          v-model="showingCurrency.Currency"
        >
          <option v-for="(currency, i) in currencyList.Currency" :key="i">{{
            currency.Currency
          }}</option>
        </select>
      </div>
      <div class="d-flex" id="currencyConvertPrice">
        <p class="text-muted">INCOME</p>
        <h3 class="bold-text" id="currencyPriceBuy">
          <span style="color:green">
            <svg
              width="1em"
              height="1em"
              viewBox="0 0 16 16"
              class="bi bi-arrow-down"
              fill="currentColor"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M4.646 9.646a.5.5 0 0 1 .708 0L8 12.293l2.646-2.647a.5.5 0 0 1 .708.708l-3 3a.5.5 0 0 1-.708 0l-3-3a.5.5 0 0 1 0-.708z"
              />
              <path
                fill-rule="evenodd"
                d="M8 2.5a.5.5 0 0 1 .5.5v9a.5.5 0 0 1-1 0V3a.5.5 0 0 1 .5-.5z"
              />
            </svg>
          </span>
          {{ income }}
        </h3>
        <div class="d-flex">
          <input
            type="number"
            v-model="myMoney"
            id="transferMoneyInput"
            placeholder="Convert Rial"
          />
          <input
            type="number"
            v-model="foreignMoney"
            id="transferMoneyInput"
            :placeholder="'Convert ' + showingCurrency.code"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
// import $ from "jquery";

export default {
  data() {
    return {
      income: 0,
      currencyList: {},
      countries: [],
      showingCurrency: {
        Currency: "",
        flag: "",
        sell: "-",
        buy: "-",
        code: "-"
      },
      myMoney: null,
      foreignMoney: null
    };
  },
  async created() {
    Axios.get("https://currency.jafarili.me/json").then(response => {
      this.currencyList = response.data;
      console.clear();
    });
  },
  watch: {
    income: function() {
      if (this.income == "NaN") {
        this.income = 0;
        alert("Please Select A Currency ");
      }
    },

    myMoney: function() {
      this.foreignMoney = null;
      this.income = this.myMoney / this.showingCurrency.sell;
      let roundedNumber = this.income.toFixed(2);
      this.income = roundedNumber;
    },

    foreignMoney: function() {
      this.myMoney = null;
      this.income = this.foreignMoney * this.showingCurrency.buy;
      let roundedNumber = this.income.toFixed(2);
      this.income = roundedNumber;
    },

    "showingCurrency.Currency": function() {
      for (let i = 0; i < this.currencyList.Currency.length; i++) {
        const selectedCurrency = this.currencyList.Currency[i];
        if (selectedCurrency.Currency == this.showingCurrency.Currency) {
          this.showingCurrency.buy = selectedCurrency.Buy;
          this.showingCurrency.sell = selectedCurrency.Sell;
          this.showingCurrency.code = selectedCurrency.Code;
          this.showingCurrency.flag = require(`../assets/flag-svg/${this.showingCurrency.Currency}.svg`);
        }
      }
    }
  }
};
</script>
<style scoped>
@import url("../assets/style/currency.css");
</style>
