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
          <td>mnemonic:</td>
          <td class="mnemonic">{{ pair.mnemonic }}</td>
        </tr>
        <tr>
          <td>private:</td>
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

window.btc = btc;
window.Mnemonic = Mnemonic;

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
      const DERIVATION = 'm/44\'/0\'/0\'/0/0'
      //Generate new mnemonic
      let code = new Mnemonic(Mnemonic.Words.ENGLISH);
      let mnemonic = code.toString();

      let xpriv = code.toHDPrivateKey();
      let derived = xpriv.derive(DERIVATION);
      let privateKey = derived.privateKey;
      let pk = new btc.PrivateKey( privateKey.toString() );

      let publicKey = new btc.PublicKey(pk);
      let address = new btc.Address(publicKey);

      console.log('from code', pk, address)

      this.pair = {
        private: pk.toWIF(),
        public: publicKey.toString(),
        address: address.toString(),
        mnemonic: mnemonic,
      };

      console.log("pair:", this.pair);
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
