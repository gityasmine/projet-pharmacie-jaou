<template>
  <div>
    <AjoutMedicament @medicamentAjoute="getMedicament" />
    <AffichMedicament ref="affichageMedicament" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import AffichMedicament from "./AffichMedicament.vue";
import AjoutMedicament from "./AjoutMedicament.vue";

const affichageMedicament = ref(null);

// Rafraîchit la liste des médicaments après un ajout ou une modification
function getMedicament() {
  affichageMedicament.value?.getMedicament();
}
</script>