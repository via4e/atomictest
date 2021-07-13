<template>
  <div>
    <!-- Check random btc address, for test 
    <div class="search">
      <input type="text" v-model="inputString" >
      <button @click="getAddressInfo(inputString)">Show Address</button>
    </div>
      -->    
    <div class="info" v-if="currentBtcAddress?.address?.id">
      <ul>
        <li><span>BTC Address:</span> {{ addr }}</li>
        <li><span>Public Key:</span> {{ pubKey }}</li>
        <li><span>Balance BTC:</span> {{ currentBtcAddress.address.balance }}</li>
        <li><span>Balance USD:</span> {{ currentBtcAddress.address.balance_usd }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import btc from 'bitcore-lib'

export default {
  name: 'ShowAddress',
  props: {
    addr: { type: String, default: "" },
    pubKey: { type: String, default: "" },
  },
  data() {
    return {
      inputString: "",
      currentBtcAddress: {}
    }
  },
  created () {
    this.getAddressInfo(this.addr)
  },
  methods: {
    async getAddressInfo (btcAddr) {

      console.log("ShowAddress addr:", btcAddr)
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
    addr() {
      console.log('ShowAddress addr watch:', this.addr)
      this.getAddressInfo(this.addr)
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
span {
  width: 200px
}
ul {
  list-style-type: none;
}
</style>
