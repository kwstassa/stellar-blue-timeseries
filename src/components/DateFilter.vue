<template>
  <!-- Flex container για την διάταξη των στοιχείων με κεντράρισμα και διαχωριστικά (gap) -->
  <div class="mb-3 d-flex flex-md-row flex-column justify-content-center">
    <!-- Ενότητα για το πεδίο Start DateTime -->
    <div class="d-flex flex-column" style="margin-right:5%">
      <!-- Ετικέτα για το πεδίο Start DateTime -->
      <label for="start" class="form-label">Start DateTime:</label>
      <!-- Input για επιλογή Start DateTime με προκαθορισμένο πλάτος -->
      <input
        type="datetime-local"
        v-model="startDate"
        id="start"
        class="form-control"
        style="width: 220px;"
      />
    </div>

    <!-- Ενότητα για το πεδίο End DateTime -->
    <div class="d-flex flex-column" style="margin-right:2%; height:38px">
      <!-- Ετικέτα για το πεδίο End DateTime -->
      <label for="end" class="form-label">End DateTime:</label>
      <!-- Input για επιλογή End DateTime με προκαθορισμένο πλάτος -->
      <input
        type="datetime-local"
        v-model="endDate"
        id="end"
        class="form-control"
        style="width: 220px;"
      />
    </div>

    <!-- Ενότητα για τα κουμπιά "Apply Filter" και "Clear Filter" -->
    <div class="d-flex flex-md-row flex-column justify-content-md-center justify-content-start">
      <!-- Κουμπί για εφαρμογή του φίλτρου -->
      <button
        @click="applyFilter"
        class="btn btn-primary text-nowrap"
        style="margin-right:5%; margin-top: 30px;"
      >
        Apply Filter
      </button>
      <!-- Κουμπί για εκκαθάριση του φίλτρου -->
      <button
        @click="clearFilter"
        class="btn btn-secondary text-nowrap"
        style="margin-right:5%; margin-top: 30px;"
      >
        Clear Filter
      </button>
    </div>
  </div>
</template>

<script>
export default {
  // Ονομασία του component
  name: 'DateFilter',
  // Τοπική κατάσταση (data) του component
  data() {
    return {
      // Αρχικά, δεν έχει επιλεγεί καμία ημερομηνία για το Start DateTime
      startDate: '',
      // Αρχικά, δεν έχει επιλεγεί καμία ημερομηνία για το End DateTime
      endDate: ''
    }
  },
  // Μέθοδοι που χειρίζονται τις ενέργειες του χρήστη
  methods: {
    // Μέθοδος για την εφαρμογή του φίλτρου ημερομηνιών
    applyFilter() {
      // Ελέγχουμε αν έχουν επιλεγεί και οι δύο ημερομηνίες
      if (this.startDate && this.endDate) {
        // Μετατρέπουμε τις ημερομηνίες από συμβολοσειρές σε αντικείμενα Date
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        // Ελέγχουμε αν η Start Date είναι μεταγενέστερη από την End Date
        if (start > end) {
          alert("Error: The start date must be before the end date.");
          return;
        }
        // Εάν οι ημερομηνίες είναι σωστές, αποστέλλουμε στο γονικό component το event 'filter-change'
        // με τις επιλεγμένες ημερομηνίες μέσα σε ένα array.
        this.$emit('filter-change', [start, end]);
      } else {
        // Εάν δεν έχουν επιλεγεί και οι δύο ημερομηνίες, ενημερώνουμε τον χρήστη
        alert("Please select both start and end dates.");
      }
    },
    // Μέθοδος για την εκκαθάριση του φίλτρου ημερομηνιών
    clearFilter() {
      // Επαναφέρουμε τις ημερομηνίες σε κενές συμβολοσειρές
      this.startDate = '';
      this.endDate = '';
      // Αποστέλλουμε στο γονικό component το event 'filter-change' με κενό array,
      // υποδεικνύοντας ότι το φίλτρο έχει καθαριστεί.
      this.$emit('filter-change', []);
    }
  }
}
</script>

