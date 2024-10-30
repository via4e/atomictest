<template>
  <div>
    <div class="info" v-if="addr">
      <table>
        <tr>
          <td>BTC Address:</td>
          <td class="normal">{{ addr }}</td>
        </tr>
        <tr>
          <td>BTC Balance:</td>
          <td class="normal balance">{{ balance }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>

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
      balance: 0,
    };
  },
  created() {
    this.getAddressInfo(this.addr);
  },
  methods: {
    async getAddressInfo(btcAddr) {
      console.log("ShowAddress addr:", btcAddr);

      //  banned by IP
      //  const f = await fetch(`https://api.blockchair.com/bitcoin/dashboards/address/${btcAddr}` );

      const DECIMAL = 100000000; // делитель, 8 нулей после точки

      const f = await fetch(
          `https://blockchain.info/unspent?active=${btcAddr}` );

      const data = await f.json();

      this.balance = data.unspent_outputs[0]?.value / DECIMAL;
      Number.isNaN(this.balance)
          ? this.balance = 0
          : null;

      console.log('balance --->', this.balance);
    },
  },
  watch: {
    addr() {
      this.getAddressInfo(this.addr);
    },
  },
};
</script>
