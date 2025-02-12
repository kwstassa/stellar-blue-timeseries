<template>
  <div>
    <!-- Αρχή Πίνακα -->
    <table class="table table-sm table-bordered table-striped table-hover">
      <thead class="table-dark">
        <tr>
          <!-- Στήλη για την ημερομηνία και ώρα -->
          <th scope="col">DateTime</th>
          <!-- Για κάθε όνομα σειράς, δημιουργούμε μια στήλη -->
          <th scope="col" v-for="series in seriesNames" :key="series">
            {{ series }}
          </th>
        </tr>
      </thead>
      <tbody>
        <!-- Εμφανίζουμε μόνο τις εγγραφές της τρέχουσας σελίδας -->
        <tr v-for="(row, rowIndex) in paginatedData" :key="row.DateTime">
          <!-- Μορφοποιημένη ημερομηνία στην πρώτη στήλη -->
          <td>{{ formatDate(row.DateTime) }}</td>
          <!-- Για κάθε σειρά, δημιουργούμε ένα πεδίο εισόδου με την αντίστοιχη τιμή -->
          <td v-for="series in seriesNames" :key="series">
            <input
              type="text"
              class="form-control"
              :value="row.values[series]"
              @change="onInputChange($event, rowIndex, series)"
            />
          </td>
        </tr>
      </tbody>
    </table>
    <!-- Τέλος Πίνακα -->

    <!-- Ελέγχοι Σελιδοποίησης -->
    <div class="d-flex justify-content-between align-items-center mt-2">
      <!-- Επιλογή αριθμού εγγραφών που θα εμφανίζονται ανά σελίδα -->
      <div>
        <label for="entriesSelect" class="me-2">Show entries:</label>
        <select
          id="entriesSelect"
          class="form-select d-inline-block"
          style="width: auto;"
          v-model.number="entriesPerPage"
          @change="resetPage"
        >
          <!-- Επιλογές για αριθμό εγγραφών -->
          <option :value="10">10</option>
          <option :value="20">20</option>
          <option :value="30">30</option>
          <!-- Το 0 σημαίνει "Όλες οι εγγραφές" -->
          <option :value="0">All entries</option>
        </select>
      </div>

      <!-- Κουμπιά για μετάβαση στην προηγούμενη και επόμενη σελίδα -->
      <div>
        <button
          class="btn btn-secondary me-2"
          :disabled="currentPage === 1"
          @click="prevPage"
        >
          Previous
        </button>
        <button
          class="btn btn-secondary"
          :disabled="currentPage >= totalPages"
          @click="nextPage"
        >
          Next
        </button>
      </div>
    </div>
    <!-- Τέλος Ελέγχων Σελιδοποίησης -->
  </div>
</template>

<script>
// Εισάγουμε τη βιβλιοθήκη dayjs για να μορφοποιήσουμε τις ημερομηνίες.
import dayjs from 'dayjs'

export default {
  // Ονομάζουμε το component ως 'DataTable'
  name: 'DataTable',
  // Ορίζουμε τις ιδιότητες (props) που δέχεται το component.
  props: {
    // Το πλήρες σύνολο δεδομένων που περνάει ο γονέας.
    data: {
      type: Array,
      required: true
    },
    // Τα ονόματα των σειρών που θα εμφανιστούν ως στήλες στον πίνακα.
    seriesNames: {
      type: Array,
      required: true
    }
  },
  // Τοπικά δεδομένα του component.
  data() {
    return {
      // Αρχική σελίδα της σελιδοποίησης. Ξεκινάμε από την πρώτη σελίδα.
      currentPage: 1,
      // Αριθμός εγγραφών ανά σελίδα. Αν είναι 0, εμφανίζονται όλες οι εγγραφές.
      entriesPerPage: 10
    }
  },
  // Computed ιδιότητες για τον υπολογισμό δεδομένων βάσει του state.
  computed: {
    // Επιστρέφει τα δεδομένα που πρέπει να εμφανιστούν για την τρέχουσα σελίδα.
    paginatedData() {
      // Αν ο χρήστης επέλεξε "Όλες οι εγγραφές" (entriesPerPage = 0), επιστρέφουμε όλο το σύνολο.
      if (this.entriesPerPage === 0) {
        return this.data;
      }
      // Υπολογισμός της θέσης εκκίνησης για τις εγγραφές της τρέχουσας σελίδας.
      const startIndex = (this.currentPage - 1) * this.entriesPerPage;
      // Επιστρέφουμε τις εγγραφές από την αρχή μέχρι τον καθορισμένο αριθμό εγγραφών.
      return this.data.slice(startIndex, startIndex + this.entriesPerPage);
    },
    // Υπολογίζει το συνολικό πλήθος σελίδων για τη σελιδοποίηση.
    totalPages() {
      // Αν εμφανίζονται όλες οι εγγραφές, θεωρούμε ότι υπάρχει μόνο μία σελίδα.
      if (this.entriesPerPage === 0) return 1;
      // Χρησιμοποιούμε το Math.ceil για να στρογγυλοποιήσουμε προς τα πάνω.
      return Math.ceil(this.data.length / this.entriesPerPage);
    }
  },
  // Μέθοδοι που χειρίζονται τις διάφορες ενέργειες του χρήστη.
  methods: {
    // Μορφοποιεί το DateTime χρησιμοποιώντας την dayjs.
    formatDate(dateString) {
      // Μορφοποίηση: 'DD-MM-YYYY HH:mm'
      return dayjs(dateString).format('DD-MM-YYYY HH:mm');
    },
    // Επεξεργάζεται την αλλαγή τιμής σε κάθε πεδίο εισόδου του πίνακα.
    // Παράμετροι:
    //   e: το γεγονός αλλαγής (event)
    //   rowIndex: ο δείκτης της σειράς στον πίνακα δεδομένων
    //   series: το όνομα της σειράς που επηρεάζεται
    onInputChange(e, rowIndex, series) {
      // Παίρνουμε τη νέα τιμή που εισήγαγε ο χρήστης.
      const newValue = e.target.value;
      // Μετατρέπουμε τη νέα τιμή σε αριθμό (float).
      const parsedValue = parseFloat(newValue);
      // Ελέγχουμε αν η τιμή δεν είναι αριθμός ή αν δεν βρίσκεται στο εύρος [-2000, 2000].
      if (isNaN(parsedValue) || parsedValue < -2000 || parsedValue > 2000) {
        // Εμφανίζουμε μήνυμα σφάλματος στον χρήστη.
        alert("Invalid input. Please enter a number between -2000 and 2000.");
        // Επαναφέρουμε την τιμή του πεδίου στην αρχική τιμή.
        e.target.value = this.data[rowIndex].values[series];
      } else {
        // Αν η τιμή είναι έγκυρη, ενημερώνουμε τον γονέα μέσω emit.
        this.$emit('update-cell', {
          rowIndex: rowIndex,
          series: series,
          value: parsedValue
        });
      }
    },
    // Επαναφέρει την σελιδοποίηση στην πρώτη σελίδα όταν αλλάζει ο αριθμός εγγραφών ανά σελίδα.
    resetPage() {
      this.currentPage = 1;
    },
    // Μεταβαίνει στην προηγούμενη σελίδα, αν δεν είμαστε ήδη στην πρώτη.
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage = this.currentPage - 1;
      }
    },
    // Μεταβαίνει στην επόμενη σελίδα, αν δεν είμαστε στην τελευταία.
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage = this.currentPage + 1;
      }
    }
  }
}
</script>

<style scoped>

</style>
