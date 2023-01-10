<template>
  <div class="calculator">
    <DisplayItem :value="displayValue"/>
    <ButtonItem label="AC" triple @onClick="clearMemory"/>
    <ButtonItem label="/" operation @onClick="setOperation"/>
    <ButtonItem label="7" @onClick="addDigit"/>
    <ButtonItem label="8" @onClick="addDigit"/>
    <ButtonItem label="9" @onClick="addDigit"/>
    <ButtonItem label="*" operation @onClick="setOperation"/>
    <ButtonItem label="4" @onClick="addDigit"/>
    <ButtonItem label="5" @onClick="addDigit"/>
    <ButtonItem label="6" @onClick="addDigit"/>
    <ButtonItem label="-" operation @onClick="setOperation"/>
    <ButtonItem label="1" @onClick="addDigit"/>
    <ButtonItem label="2" @onClick="addDigit"/>
    <ButtonItem label="3" @onClick="addDigit"/>
    <ButtonItem label="+" operation @onClick="setOperation"/>
    <ButtonItem label="0" double @onClick="addDigit"/>
    <ButtonItem label="." @onClick="addDigit"/>
    <ButtonItem label="=" operation @onClick="setOperation"/>
  </div>
</template>

<script>
import DisplayItem from "../components/DisplayItem.vue"
import ButtonItem from "../components/ButtonItem.vue"

export default {
  data: function () {
    return {
      displayValue: "0",
      clearDisplay: false,
      operation: null,
      values: [0, 0],
      current: 0
    }
  },
  components: {ButtonItem, DisplayItem},
  methods: {
    clearMemory() {
      Object.assign(this.$data, this.$options.data())
    },
    setOperation(operation) {
      if (this.current === 0) {
        this.operation = operation
        this.current = 1
        this.clearDisplay = true
      } else {
        const equals = operation === "="
        const currentOperation = this.operation

        try {
          this.values[0] = eval(
            `${this.values[0]} ${currentOperation} ${this.values[1]}`
          )
        } catch (e) {
          this.$emit('onError', e)
        }

        this.values[1] = 0

        this.displayValue = this.values[0]
        this.operation = equals ? null : operation
        this.current = equals ? 0 : 1
        this.clearDisplay = !equals
      }
    },
    addDigit(n) {
      if (n === "." && this.displayValue.includes(".")) {
        return 
      }

      const clearDisplay = this.displayValue === "0"
        || this.clearDisplay
      const currentValue = clearDisplay ? "" : this.displayValue
      const displayValue = currentValue + n

      this.displayValue = displayValue
      this.clearDisplay = false

      if (n !== ".") {
        const i = this.current
        const newValue = parseFloat(displayValue)
        this.values[i] = newValue
      }
    }
  }

}
</script>

<style>
.calculator{
    height: 320px;
    width: 235px;
    border-radius: 5px;
    overflow: hidden;

    display: grid;
    grid-template-columns: repeat(4, 25%);
    grid-template-rows: 1fr 48px 48px 48px 48px 48px;
}
</style>