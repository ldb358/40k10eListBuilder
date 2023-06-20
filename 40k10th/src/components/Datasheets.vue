<script setup lang="ts">
const emit = defineEmits(['factionReady', 'dataImported'])
</script>

<template>
  <div class="factions">
    <h3>
      Select a faction:
    </h3>
    <select v-model="selectedFaction">
      <option v-for="faction in factions" v-bind:value="{ name: faction.name, file: faction.file }">
         {{ faction.name }}
       </option>
    </select>
    <button @click="getFactionData()">Build From scratch</button>
    <textarea v-model="list"></textarea>
    <button @click="importList()">Import List</button>

    <div class="errorMessage" v-if="error">{{ error }}</div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
interface Unit {
  total: number,
  id: number, 
  unit: string,
  selectedOption: string,
  selectedUpgrade: string, 
  notes: string
}

export default {
  name: "App",
  data() {
    return {
      items: [],
      selectedFaction: {name: "", file: ""},
      error: "",
      list: "",
      factions: [
        {"name": "Orks", "file": "orks.json"},
        {"name": "Adeptus Mechanicus", "file": "admech.json"},
        {"name": "Adeptus Custodes", "file": "custodes.json"},
        {"name": "World Eaters", "file": "worldeaters.json"},
        {"name": "Not Orks", "file": ""},
    ]
    };
  },
  methods: {
    async loadFactionData() {
      if (this.selectedFaction.file === "") {
        this.error = "Select a valid faction"
        return
      }
      this.error = ""
      const res = await axios.get(`/datafiles/` + this.selectedFaction.file);
      if (res.status !==  200) {
        this.error = "could not load datafile try again later"
        return
      }
      return res.data
    },
    async getFactionData() {
      var data = await this.loadFactionData()
      console.log(data)
      this.$emit('factionReady', this.selectedFaction.name, data)
    },
    async importList(){
      this.error = ""
      if (this.selectedFaction.name === "") {
        this.error = "make sure to select the faction you would like to import"
        return
      }
      if (this.list === "") {
        this.error = "paste an exported list above"
        return
      }
      var lines = this.list.split("\n")
      console.log(lines)
      if (!lines[0].startsWith("Faction: ")) {
        this.error = "invalid file format should start with the faction"
        return
      }
      var faction = lines[0].substring(9)
      console.log("Faction: ", faction)
      //skip the next two lines
      var units = []
      for (var i=3; i < lines.length; ++i) {
        // skip empty lines
        if (lines[i] === "") {
          continue
        }
        var unit = this.parseLine(lines[i])
        if (unit.unit !== "") {
          unit.id = i
          units.push(unit)
        }
      }
      var data = await this.loadFactionData()
      this.$emit("dataImported",  this.selectedFaction.name, data, units)
    },
    parseLine(line: string): Unit {
      //Beast Snagga Boyz - 10 models - None - 105 -- 
      var data = line.match(/(.+) - (.+) - (.+) = (.+) --(.*)/)
      if (data == null || data.length != 6) {
        this.error = `could not parse line: ${line}`
        return {total: 0, id: 0, unit: "", selectedOption: "", selectedUpgrade: "", notes: ""}
      }
      return {total: parseInt(data[4]), id: 0, unit: data[1], selectedOption: data[2], selectedUpgrade: data[3], notes: data[5]}
    }
  }
};
</script>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.factions h1,
.factions h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .factions h1,
  .factions h3 {
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
textarea {
  width: 500px;
  height: 300px;
  float: left;
  clear: left;
}

.errorMessage {
  color: red;
  float: left;
  clear: both;
  font-size: 20px;
}
</style>