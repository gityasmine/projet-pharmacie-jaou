<template>
  <div>
    <h2>Ajouter un médicament</h2>
    <form @submit.prevent="ajouterMedicament">
      <label>Nom :</label>
      <input v-model="nouveauMedicament.denomination" required />

      <label>Forme pharmaceutique :</label>
      <input v-model="nouveauMedicament.formepharmaceutique" required />

      <label>Quantité :</label>
      <input type="number" v-model="nouveauMedicament.qte" min="1" required />

      <label>Photo :</label>
      <input type="file" @change="handleFileUpload" accept="image/*" />

      <button type="submit">Ajouter</button>
    </form>
    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script setup>
import { ref, defineEmits } from "vue";

const emit = defineEmits(["medicamentAjoute"]);
const idpharmacie = 9;
const url = `https://apipharmacie.pecatte.fr/api/${idpharmacie}/medicaments`;

const nouveauMedicament = ref({
  denomination: "",
  formepharmaceutique: "",
  qte: 1,
  photo: null
});

const message = ref("");

// Envoie une requête pour ajouter un médicament
async function ajouterMedicament() {
  try {
    const response = await fetch(url, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(nouveauMedicament.value),
    });

    const data = await response.json();
    if (data.status === 1) {
      message.value = "Médicament ajouté avec succès";
      resetForm();
      emit("medicamentAjoute"); // Rafraîchit la liste des médicaments
    } else {
      message.value = "Erreur : " + data.message;
    }
  } catch (error) {
    console.error("Erreur lors de l'ajout :", error);
    message.value = "Une erreur est survenue";
  }
}

// Réinitialise le formulaire après ajout
function resetForm() {
  nouveauMedicament.value = { denomination: "", formepharmaceutique: "", qte: 1, photo: null };
}

// Gère le chargement de l'image et l'encode en Base64
function handleFileUpload(event) {
  const file = event.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    nouveauMedicament.value.photo = reader.result;
  };
  reader.readAsDataURL(file);
}
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

button {
  margin-top: 10px;
}
</style>