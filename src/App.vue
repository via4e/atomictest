<template>
  <img alt="Vue logo" src="./assets/logo.png" width="90" height="90">
  <ShowAddress />
  <hr />

  <hr />
  <button @click="createPair()">Create</button>
  <button>Restore</button>
</template>

<script>
import ShowAddress from './components/ShowAddress.vue'
import bitcore from 'bitcore-lib'

export default {
  name: 'App',
  components: {
    ShowAddress
  },
  methods: {
    createPair () {
      let privateKey = new bitcore.PrivateKey();
      console.log(privateKey)
      let address = privateKey.toAddress()
      console.log('addr:',address)
    }
  }
}
</script>
