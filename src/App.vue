<template>
  <div>
    <img alt="Vue logo" src="./assets/logo.png" width="90" height="90" />
    <ShowAddress
      :addr="pair.address"
      :pubKey="pair.public"
      @clearPair="clearPair"
    />
    <hr />
    <template v-if="pair?.private && pair.private.length">
      {{ pairMessage }}
      <table>
        <tr>
          <td>private:</td>
          <!-- <td>{{ pair.private.slice(0,9)}} ...</td>  - if hide private key, we cant copy it to restore -->
          <td>{{ pair.private }}</td>
        </tr>
        <tr>
          <td>public:</td>
          <td>{{ pair.public }}</td>
        </tr>
        <tr>
          <td>address:</td>
          <td>{{ pair.address }}</td>
        </tr>
      </table>
    </template>
    <hr />
    <button @click="createPair()">Create</button>
    <button @click="createMnemonic()">Create Mnemonic</button>
    <button @click="restoreDialog()">Restore</button>

    <Restore
      v-if="showRestore"
      @restoreKey="restorePublicKey"
      @cancelRestore="restoreCancel"
    />
  </div>
</template>

<script>
import ShowAddress from "./components/ShowAddress.vue";
import Restore from "./components/Restore.vue";
import btc from "bitcore-lib";
import Mnemonic from "bitcore-mnemonic";

export default {
  name: "App",
  components: {
    ShowAddress,
    Restore,
  },
  data() {
    return {
      pair: {},
      inputPrivate: "",
      showRestore: false,
      pairMessage: "Pair created..."
    };
  },
  methods: {
    createPair() {
      let privKey = new btc.PrivateKey();
      let pubKey = new btc.PublicKey(privKey);
      let addr = privKey.toAddress();

      this.pair = {
        private: privKey.toWIF(),
        public: pubKey.toString(),
        address: addr.toString(),
      };

      console.log("pair:", this.pair);
    },
    createMnemonic() {
      let seed = new Mnemonic();
      let privKey = new btc.PrivateKey ( seed.toHDPrivateKey(seed.toString()) );
      let pubKey = new btc.PublicKey( privKey );
      //let addr = privKey.toAddress();

      this.pair = {
        seed: seed.toString(),
        private: privKey.toString(), //.toWIF(),
        public: pubKey,
        //address: addr.toString(),
      };

      console.log(this.pair);
    },
    restoreDialog() {
      console.log(this.privateKey, this.publicKey, this.address);
      this.showRestore = true;
    },
    restorePublicKey(str) {
      console.log("restorePublicKey str", str);

      if (btc.PrivateKey.isValid(str)) {
        let privKey = btc.PrivateKey.fromWIF(str);
        let pubKey = new btc.PublicKey(privKey);
        let addr = privKey.toAddress();

        this.pair = {
          private: str,
          public: pubKey.toString(),
          address: addr.toString(),
        };

        this.pairMessage = "Pair restored..."
      } else {
        console.error('invalid private key, can not restore public key..')
      }
      this.showRestore = false;
    },
    restoreCancel() {
      console.log("restore");
      this.showRestore = false;
    },
    clearPair() {
      this.pair = {}
    },
  },
};
</script>
