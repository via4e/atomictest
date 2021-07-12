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
</template>

<script>
import btc from 'bitcore-lib'

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
      const result = btc.Address.isValid(btcAddr)
      
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
