<template>
<div>
  <img alt="Vue logo" src="./assets/logo.png" width="90" height="90">
  <ShowAddress v-if="pair?.address && pair.address.length" :addr="pair.address" :pubKey="pair.public"/>
  <hr />
  <template v-if="pair?.private && pair.private.length">
    Pair:<br/>
    {{ pair }}
  </template>
  <hr />
  <button @click="createPair()">Create</button>
  <button @click="restoreDialog()">Restore</button>

  <Restore 
    v-if="showRestore"
    @restoreKey="restorePublicKey"
    @cancelRestore="restoreCancel" 
  />

</div>
</template>

<script>
import ShowAddress from './components/ShowAddress.vue'
import Restore from './components/Restore.vue'
import btc from 'bitcore-lib'

export default {
  name: 'App',
  components: {
    ShowAddress, Restore
  },
  data () {
    return {
      pair: {},
      inputPrivate: "",
      showRestore: false
    }
  },
  methods: {
    createPair () {
      // bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh

      let privKey = new btc.PrivateKey()
      let pubKey = new btc.PublicKey(privKey)
      let addr = privKey.toAddress()

      this.pair = {
        private: privKey.toWIF(),
        public: pubKey.toString(),
        address: addr.toString()
      }

      console.log('pair:', this.pair)
    },
    restoreDialog () {
      console.log(this.privateKey, this.publicKey, this.address)
      this.showRestore = true
    },
    restorePublicKey(str) {
      console.log("restore str", str)
      
      if (btc.PrivateKey.isValid(str)){
        //L3T1s1TYP9oyhHpXgkyLoJFGniEgkv2Jhi138d7R2yJ9F4QdDU2m valid private
          let privKey = btc.PrivateKey.fromWIF(str)
          let pubKey = new btc.PublicKey(privKey)
          let addr = privKey.toAddress()

          this.pair = {
            private: str,
            public: pubKey.toString(),
            address: addr.toString()
          }
      }
      this.showRestore = false
    },
    restoreCancel() {
      console.log('restore')
      this.showRestore = false
    },
    doAddr(priv) {
      return priv.toAddress()
    }
  },
}
</script>
