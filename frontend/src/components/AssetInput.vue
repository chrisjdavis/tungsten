<template>
  <div>
    <div class="w-row">
      <div class="w-col w-col-9">
        <input v-bind="$attrs" type="number" class="text-field w-input numeric-input" maxlength="200"
               min="0" step="0.0001" :value="numericValue" @input="propagateValue($event)"/>
      </div>
      <div class="w-col w-col-3 unit-col">
        EOS
      </div>
    </div>
  </div>
</template>

<script>
import eosjs from "eosjs";
import Big from "big.js";

const BigEos = Big();
BigEos.RM = 0;
BigEos.PE = 100;

export default {
  name: "AssetInput",
  inheritAttrs: false,
  props: ["value"],
  computed: {
    numericValue() {
      return (
        this.value &&
        BigEos(eosjs.modules.format.parseAsset(this.value).amount).toString()
      );
    }
  },
  methods: {
    propagateValue(event) {
      if (event.target.value) {
        let [integer, decimal] = event.target.value.split(".");
        if (decimal && decimal.length > 4) {
          decimal = decimal.slice(0, 4);
        }
        const value = integer + (decimal ? `.${decimal}` : "");
        event.target.value = value;
        this.$emit("input", BigEos(value).toFixed(4) + " EOS");
      } else {
        this.$emit("input", "");
      }
    }
  }
};
</script>

<style scoped>
.numeric-input {
  text-align: right;
}
.unit-col {
  padding-top: 15px;
}
</style>
