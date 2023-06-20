<script setup lang="ts">
import UnitSelect from './UnitSelect.vue'
</script>

<template>
  <div class="list-builder">
    <h2>Total Cost: {{ total }}</h2>
    <header>
      <div class="col">Unit</div>
      <div class="col">Qty</div>
      <div class="col">Upgrades</div>
      <div class="col">Notes</div>
      <div class="col">Cost</div>
      <div class="col"></div>
    </header>
    <div class="row" v-for="unit in units">
      <UnitSelect :key="unit.id" :id="unit.id" :unit="unit.unit" :option="unit.selectedOption" :upgrade="unit.selectedUpgrade" :notes="unit.notes" :faction-data="factionData" @update-total="updateTotal" @remove="removeItem"/>
    </div>
    <button @click="addUnit()">Add Unit</button>
    <button @click="exportList()">Export List</button>
    <div class="errorMessage" v-if="error">{{ error }}</div>
  </div>
  <div class="listbox" v-if="exportedList !== ''">
    <div class="content-area">
      <button @click="exportedList = ''">Close</button>
      <textarea>{{ exportedList }}</textarea>
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { toHandlers } from "vue";
export default {
  name: "App",
  props: ["factionData", "importedUnits"],
  data() {
    var units = [{total: 0, id: 0,  unit: "", selectedOption: "", selectedUpgrade: "", notes: ""}]
    if (this.importedUnits !== null && this.importedUnits.length > 0) {
      units = this.importedUnits
    }
    var count = units.reduce((total, cur) => Math.max(total, cur.id), 0) + 1
    return {
      error: "",
      units: units,
      //never reduce count, this ensures unique values while allowing items to be removed
      count: count,
      total: 0,
      exportedList: ""
    };
  },
  methods: {
    addUnit() {
      this.units.push({total: 0, id: this.count, unit: "", selectedOption: "", selectedUpgrade: "", notes: ""})
      this.count++
      console.log(this.count, this.units);
    },
    updateTotal(id: number, unit: string, selectedOption: string, selectedUpgrade: string, notes: string, value: number) {
      for (var u of this.units) {
        if (u.id == id) {
          u.total = value
          u.unit = unit
          u.selectedOption = selectedOption
          u.selectedUpgrade = selectedUpgrade
          u.notes = notes
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
      var list = `Faction: ${this.factionData.faction}\nTotal: ${this.total} points\n\n`
      this.units.forEach(unit => {
        var upgrade = unit.selectedUpgrade == "" ? "None" : unit.selectedUpgrade
        list += `${unit.unit} - ${unit.selectedOption} - ${upgrade} = ${unit.total} -- ${unit.notes}\n`
      })
      this.exportedList = list
    },
  }
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
  text-align: center;
  font-size: 20px;
}

.col {
  flex: 1;        /* distributes space on the line equally among items */
}
.listbox {
  position: fixed;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0,0,0,.5);
  padding: 0;
  margin: 0;
}
.listbox .content-area {
  width: 100%;
  height: 100%;
}
.listbox .content-area textarea {
  min-height: 25%;
  width: 100%;
}
.listbox .content-area button {
  float: right;
}

@media (min-width: 1024px) {
  .listbox .content-area {
    width: 50%;
    height: 100%;
  }
}
</style>