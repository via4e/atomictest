<template>
  <div class="popup">
    Enter private key and click Ok to restore public key
    <hr />

    <input
      type="text"
      v-model="inputString"
      placeholder="enter a 12 word phrase.."
    />

    <hr />
    <button @click.stop="restoreKey()">Ок</button>
    <button @click.stop="cancel()">Cancel</button>
  </div>
</template>

<script>
export default {
  name: "Restore",
  data() {
    return {
      inputString: "",
    };
  },
  methods: {
    cancel() {
      this.$emit("cancelRestore");
    },
    restoreKey() {
      this.$emit("restoreKey", this.inputString);
    },
    restoreMnemoKey() {
      this.$emit("restoreMnemoKey", this.inputString);
    },
  },
};
</script>

