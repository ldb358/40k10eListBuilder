<template>
    <select class="col" v-model="selectedUnit">
      <option v-for="(_, name)  in factionData.units">
        {{ name }}
      </option>
    </select>
    <select class="col" v-model="selectedOption">
      <option v-for="(_, name)  in unitOptions">
        {{ name }}
      </option>
    </select>
    <select class="col" v-model="selectedUpgrade">
      <option>None</option>
      <option v-for="(val, name)  in factionData.upgrades">
        {{ name }}
      </option>
    </select>
    <textarea v-model="addedNotes" @change="updateData"></textarea>
    <div class="col">{{ total }}</div>
    <span class="col delete" @click="remove"><img width="25" src="/x-icon.png" alt="x" /></span>
</template>

<script lang="ts">
import axios from "axios";
import { onUpdated, toHandlers } from "vue";
export default {
  name: "UnitSelect",
  props: ["factionData", "id", "unit", "option", "upgrade", "notes"],
  emits: ["updateTotal", "remove"],
  data() {
    var unitOptions = []
    if (this.unit !== "") {
      console.log("Units:", this.factionData)
      unitOptions = this.factionData.units[this.unit]
    }
    var total = 0
    if (this.unit != "" && this.option != "") {
      total = this.calcSubtotal(this.unit, this.option, this.upgrade)
    }
    return {
      error: "",
      total: total,
      unitOptions: unitOptions,
      addedNotes: this.notes,
      selectedUnit: this.unit,
      selectedOption: this.option,
      selectedUpgrade: this.upgrade,
    };
  },
  watch: {
    selectedUnit: function() {
      console.log("unit updated")
      this.total = 0
      this.unitOptions = []
      this.unitOptions = this.factionData.units[this.selectedUnit]
    },
    selectedOption: function() {
      this.updateData()
    },
    selectedUpgrade: function() {
      this.updateData()
    }
  },
  methods: {
    remove() {
      this.$emit("remove", this.id)
    },
    calcSubtotal(unit: string, option: string, upgrade: string) {
      var upgradeCost: any = 0
      if (upgrade !== "None" &&  upgrade !== "") {
        upgradeCost = this.factionData.upgrades[upgrade]
      }
      return this.factionData.units[unit][option] +parseInt(upgradeCost)
    },
    updateData() {
      this.total = this.calcSubtotal(this.selectedUnit, this.selectedOption, this.selectedUpgrade)
      this.$emit("updateTotal", this.id,  this.selectedUnit, this.selectedOption, this.selectedUpgrade, this.addedNotes, this.total)
    }
  },
};
</script>

<style scoped>
.list-builder {
  float: left;
  width: 100%;
}
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.list-builder h1,
.list-builder h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .list-builder h1,
  .list-builder h3 {
    text-align: left;
  }
}
select {
  width: 200px;
  height: 50px;
  font-size: 20px;
  float: left;
  border-right: none;
  padding: 0px;
}
button {
  width: 200px;
  height: 50px;
  float: left;
  padding: 0px;
  margin: 0px;
  border: 1px solid black;
  border-left: none;
  background-color: hsla(160, 100%, 37%, 1);
  color: white;
  font-size: 20px;
  cursor: pointer;
}

.errorMessage {
  color: red;
  float: left;
  clear: both;
  font-size: 20px;
}

header, .row {
  display: flex;  /* aligns all child elements (flex items) in a row */
  width: 100%;
}

.col {
  flex: 1;        /* distributes space on the line equally among items */
}

.delete {
  display: flex;
  align-items: center;
  cursor: pointer;
}
</style>