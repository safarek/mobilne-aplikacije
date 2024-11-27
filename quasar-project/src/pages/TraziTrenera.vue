<template>
  <q-page padding>
    <!-- Pretraga -->
    <div class="q-mb-md">
      <q-input
        v-model="searchQuery"
        label="Unesite pojam za pretragu"
        filled
        class="q-mb-md"
        debounce="300"
      />

      <div class="q-mb-md">
        <q-checkbox
          v-model="searchByName"
          label="Pretražuj po imenu"
        />
        <q-checkbox
          v-model="searchBySpecialty"
          label="Pretražuj po specijalnosti"
        />
      </div>

      <q-btn @click="performSearch" label="Traži" color="primary" />

      <!-- Tablica sa rezultatima -->
      <q-table
        :rows="filteredTrainers"
        :columns="columns"
        row-key="id"
        class="q-mt-md"
      >
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td :props="props" prop="name" />
            <q-td :props="props" prop="specialty" />
            <q-td :props="props" prop="experience" />
          </q-tr>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script>
import { ref, computed, watch } from "vue";

export default {
  name: "PretrazivanjeTrenera",
  setup() {
    // Dummy podaci za fitness trenere
    const trainers = ref([
      { id: 1, name: "Marko Horvat", specialty: "Kardio", experience: "5 godina" },
      { id: 2, name: "Ana Novak", specialty: "Yoga", experience: "3 godine" },
      { id: 3, name: "Ivan Kovačić", specialty: "Snaga i kondicija", experience: "7 godina" },
      { id: 4, name: "Lucija Matić", specialty: "Pilates", experience: "4 godine" },
    ]);

    // Statička pretraga
    const searchQuery = ref("");
    const searchByName = ref(true);
    const searchBySpecialty = ref(false);

    // Kolone tablice
    const columns = [
      { name: "name", label: "Ime", required: true, align: "left", field: "name" },
      { name: "specialty", label: "Specijalnost", required: true, align: "left", field: "specialty" },
      { name: "experience", label: "Iskustvo", required: false, align: "left", field: "experience" },
    ];

    // Funkcija za obavljanje pretrage
    const performSearch = () => {
      console.log("Performing search with query:", searchQuery.value);
      filteredTrainers.value = trainers.value.filter((trainer) => {
        const searchText = searchQuery.value.toLowerCase();
        let matches = false;

        // Ako je odabrano pretraživanje po imenu
        if (searchByName.value && trainer.name.toLowerCase().includes(searchText)) {
          matches = true;
        }

        // Ako je odabrano pretraživanje po specijalnosti
        if (searchBySpecialty.value && trainer.specialty.toLowerCase().includes(searchText)) {
          matches = true;
        }

        return matches;
      });
      console.log("Filtered trainers:", filteredTrainers.value);
    };

    // Reaktivno praćenje promjena unosa u pretragu
    const filteredTrainers = ref(trainers.value);

    // Watch za praćenje promjena unosa i checkboxova
    watch([searchQuery, searchByName, searchBySpecialty], () => {
      performSearch(); // Automatski pokreće pretragu kad god se promijene ovi podaci
    });

    return {
      searchQuery,
      searchByName,
      searchBySpecialty,
      columns,
      filteredTrainers,
      performSearch,
    };
  },
};
</script>

<style scoped>
/* Stilizacija inputa i tablice */
.q-table {
  min-height: 300px;
}

.q-checkbox {
  margin-right: 20px;
}
</style>
