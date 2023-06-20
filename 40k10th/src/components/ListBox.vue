<script setup lang="ts">
import UnitSelect from './UnitSelect.vue'
</script>

<template>
  <div class="box">
    <text></text>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { toHandlers } from "vue";
export default {
  name: "App",
  props: ["factionData"],
  data() {
    return {
      error: "",
      units: [{total: 0, id: 0,  unit: "", selectedOption: "", selectedUpgrade: ""}],
      //never reduce count, this ensures unique values while allowing items to be removed
      count: 1,
      total: 0,
    };
  },
  methods: {
    addUnit() {
      this.units.push({total: 0, id: this.count, unit: "", selectedOption: "", selectedUpgrade: ""})
      this.count++
      console.log(this.count, this.units);
    },
    updateTotal(id: number, unit: string, selectedOption: string, selectedUpgrade: string, value: number) {
      for (var u of this.units) {
        if (u.id == id) {
          u.total = value
          u.unit = unit
          u.selectedOption = selectedOption
          u.selectedUpgrade = selectedUpgrade
          break
        }
      }
      
      this.calcTotal()
    },
    calcTotal() {
      var newTotal = 0
      this.units.forEach(e => {
        newTotal += e.total
      })
      this.total = newTotal
    },
    removeItem(id: number) {
      for (var i=this.units.length; i--; ) {
        if (this.units[i].id === id) {
          this.units.splice(i, 1);
          break
        }
      }
      this.calcTotal();
    },
    exportList() {

    },
  }
};
</script>

<style scoped>

</style>