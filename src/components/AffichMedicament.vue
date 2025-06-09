<template>
  <div>
    <h2 class="text-center">Liste des Médicaments</h2>

    <input type="text" v-model="searchQuery" placeholder="Rechercher un médicament..." class="search-bar" />

    <div class="medicament-container">
      <div v-for="medicament in filteredMedicaments" :key="medicament.id" class="medicament-card">
        <img :src="getImageUrl(medicament.photo)" alt="Image médicament" v-if="medicament.photo">
        <h3>{{ medicament.denomination }}</h3>
        <p><strong>Forme :</strong> {{ medicament.formepharmaceutique }}</p>
        <p><strong>Quantité :</strong> {{ medicament.qte }}</p>

        <div class="quantity-buttons">
          <button class="increment" @click="modifierQuantite(medicament.id, medicament.qte + 1)">+1</button>
          <button class="decrement" @click="modifierQuantite(medicament.id, medicament.qte - 1)" :disabled="medicament.qte <= 0">-1</button>
        </div>
        <button class="supprimer" @click="supprimerMedicament(medicament.id)">Supprimer</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, defineExpose } from "vue";
import { Medicament } from "../Medicament.js";

const idpharmacie = 9;
const url = `https://apipharmacie.pecatte.fr/api/${idpharmacie}/medicaments`;

const listeMedicament = ref([]);
const searchQuery = ref("");

// Récupère la liste des médicaments depuis l'API
async function getMedicament() {
  try {
    const response = await fetch(url);
    const data = await response.json();
    if (Array.isArray(data)) {
      listeMedicament.value.splice(0, listeMedicament.value.length, ...data.map(f => new Medicament(f)));
    }
  } catch (error) {
    console.error("Erreur lors de la récupération des médicaments :", error);
  }
}

// Modifie la quantité d'un médicament en envoyant une requête PUT
async function modifierQuantite(id, nouvelleQte) {
  if (nouvelleQte < 0) return;

  try {
    const response = await fetch(url, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ id, qte: nouvelleQte }),
    });

    const data = await response.json();
    if (data.status === 1) {
      const medicament = listeMedicament.value.find(m => m.id === id);
      if (medicament) medicament.qte = nouvelleQte;
    }
  } catch (error) {
    console.error("Erreur lors de la modification de la quantité :", error);
  }
}

// Supprime un médicament en envoyant une requête DELETE
async function supprimerMedicament(id) {
  if (!confirm("Voulez-vous vraiment supprimer ce médicament ?")) return;

  try {
    const response = await fetch(`${url}/${id}`, { method: "DELETE" });
    const data = await response.json();
    if (data.status === 1) {
      listeMedicament.value = listeMedicament.value.filter(m => m.id !== id);
    }
  } catch (error) {
    console.error("Erreur lors de la suppression :", error);
  }
}

// Filtrage des médicaments selon la recherche
const filteredMedicaments = computed(() => {
  return listeMedicament.value.filter(medicament =>
      medicament.denomination.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

onMounted(getMedicament);
defineExpose({ getMedicament });

// Génère l'URL complète de l'image
function getImageUrl(photo) {
  return photo ? `https://apipharmacie.pecatte.fr/images/${photo}` : "";
}
</script>

<style scoped>
.search-bar {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.medicament-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
  padding: 20px;
}

.medicament-card {
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
  text-align: center;
  transition: transform 0.2s, box-shadow 0.2s;
}

.medicament-card:hover {
  transform: scale(1.03);
  box-shadow: 2px 2px 15px rgba(0, 0, 0, 0.2);
}

.medicament-card img {
  width: 100px;
  height: 100px;
  object-fit: cover;
  border-radius: 10px;
  margin-bottom: 10px;
}

.medicament-card h3 {
  margin: 10px 0;
  font-size: 20px;
  color: #2e7d32;
}

.medicament-card p {
  margin: 5px 0;
  font-size: 14px;
}

button {
  border: none;
  padding: 10px 15px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: background 0.2s, transform 0.1s;
}

button:hover {
  transform: scale(1.05);
}

button.modifier {
  background-color: #1976d2;
  color: white;
}

button.supprimer {
  background-color: #d32f2f;
  color: white;
}

button.increment {
  background-color: #4caf50;
  color: white;
}

button.decrement {
  background-color: #ff9800;
  color: white;
}

button:disabled {
  background-color: gray;
  cursor: not-allowed;
}

.quantity-buttons {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 10px;
}
</style>