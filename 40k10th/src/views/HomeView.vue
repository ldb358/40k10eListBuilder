<script setup lang="ts">
import Datasheets from '../components/Datasheets.vue'
import List from '../components/List.vue'
</script>

<template>
  <main>
    <Datasheets v-if="faction === ''" @factionReady="factionReady" @dataImported="dataImported" />
    <List v-if="faction !== ''" :faction-data="factionData" :imported-units="importedUnits"/>
  </main>
</template>

<script lang="ts">
export default {
  data() {
    return {
      "factionData": {},
      importedUnits: [],
      faction: "",
    }
  },
  methods: {
    factionReady(faction: string, factionData: object) {
      console.log(faction, factionData)
      this.faction = faction
      this.factionData = factionData
    },
    dataImported(faction: string, factionData: object, units: any) {
      console.log(faction, factionData)
      this.faction = faction
      this.factionData = factionData
      this.importedUnits = units
    }
  }
}
</script>
