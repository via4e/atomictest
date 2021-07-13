<template>
  <div>
    <div class="info" v-if="currentBtcAddress?.address?.id">
      <table>
        <tr>
          <td>BTC Address:</td>
          <td>{{ addr }}</td>
        </tr>
        <tr>
          <td>Public Key:</td>
          <td>{{ pubKey }}</td>
        </tr>
        <tr>
          <td>Balance BTC:</td>
          <td>{{ currentBtcAddress.address.balance }}</td>
        </tr>
        <tr>
          <td>Balance USD:</td>
          <td>{{ currentBtcAddress.address.balance_usd }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
import btc from "bitcore-lib";

export default {
  name: "ShowAddress",
  props: {
    addr: { type: String, default: "" },
    pubKey: { type: String, default: "" },
  },
  data() {
    return {
      inputString: "",
      currentBtcAddress: {},
    };
  },
  created() {
    this.getAddressInfo(this.addr);
  },
  methods: {
    async getAddressInfo(btcAddr) {
      console.log("ShowAddress addr:", btcAddr);
      const f = await fetch(
        `https://api.blockchair.com/bitcoin/dashboards/address/${btcAddr}`
      );
      const data = await f.json();
      const result = btc.Address.isValid(btcAddr);

      if (result) {
        this.currentBtcAddress = data.data[btcAddr];
        this.currentBtcAddress.address.id = btcAddr;
        console.log("current:", this.currentBtcAddress.context);
      } else {
        console.log("Wrong BTC Address");
        this.currentBtcAddress = {};
      }

      console.log(data);
    },
  },
  watch: {
    addr() {
      this.getAddressInfo(this.addr);
    },
  },
};
</script>
