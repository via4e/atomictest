<template>
  <div>
    <div class="search">
      <input type="text" v-model="inputString" >
      <button @click="getAddressInfo(inputString)">Show Address</button>
    </div>
    <div class="info" v-if="currentBtcAddress?.address?.id">
      <ul>
        <li>Текущий адрес: {{ currentBtcAddress.address.id }}</li>
        <li>Баланс BTC: {{ currentBtcAddress.address.balance }}</li>
        <li>Баланс USD: {{ currentBtcAddress.address.balance_usd }}</li>
        <li>Принято BTC: {{ currentBtcAddress.address.received }}</li>
        <li>Принято USD: {{ currentBtcAddress.address.received_usd }}</li>
        <li>Потрачено BTC: {{ currentBtcAddress.address.spent }}</li>
        <li>Потрачено USD: {{ currentBtcAddress.address.spent_usd }}</li>
      </ul>
    </div>
  </div>

  <!-- 
balance: 1328205468
balance_usd: 455574.475524
first_seen_receiving: "2011-06-21 01:21:42"
first_seen_spending: null
id: "1BitcoinEaterAddressDontSendf59kuE"
last_seen_receiving: "2021-07-04 04:27:53"
last_seen_spending: null
output_count: 392
received: 1328205468
received_usd: 9101.9635
script_hex: "76a914759d6677091e973b9e9d99f19c68fbf43e3f05f988ac"
scripthash_type: null
spent: 0
spent_usd: 0
transaction_count: 391
type: "pubkeyhash"
unspent_output_count: 392

  -->
</template>



<script>
export default {
  name: 'ShowAddress',
  props: {
    msg: String
  },
  data() {
    return {
      inputString: "",
      currentBtcAddress: {}
    }
  },
  methods: {
    async getAddressInfo (btcAddr) {
      console.log("addr:", btcAddr)
      const f = await fetch (
        `https://api.blockchair.com/bitcoin/dashboards/address/${btcAddr}`
      )
      const data = await f.json()
      const result = data.context.results

      if (result) {
        this.currentBtcAddress = data.data[btcAddr]
        this.currentBtcAddress.address.id = btcAddr
        console.log("current:", this.currentBtcAddress.context)
      } else {
        console.log('Wrong BTC Address')
        this.currentBtcAddress = {}
      }

      console.log(data)
    }
  },
  watch: {
    currentBtcAddress() {
      console.log('w:', this.currentBtcAddress)
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
