<template>
  <div class="mb-3">
    <div class="row align-items-end">
      <!-- Πεδίο εισόδου για την αρχική ημερομηνία/ώρα -->
      <div class="col-md-4">
        <label for="start" class="form-label">Start DateTime:</label>
        <input
          type="datetime-local"
          v-model="startDate"
          id="start"
          class="form-control"
        />
      </div>
      <!-- Πεδίο εισόδου για την τελική ημερομηνία/ώρα -->
      <div class="col-md-4">
        <label for="end" class="form-label">End DateTime:</label>
        <input
          type="datetime-local"
          v-model="endDate"
          id="end"
          class="form-control"
        />
      </div>
      <!-- Κουμπιά για εφαρμογή και καθαρισμό του φίλτρου -->
      <div class="col-md-4">
        <button @click="applyFilter" class="btn btn-primary me-2">
          Apply Filter
        </button>
        <button @click="clearFilter" class="btn btn-secondary">
          Clear Filter
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DateFilter',
  data() {
    return {
      startDate: '',
      endDate: ''
    }
  },
  methods: {
    applyFilter() {
      // Ελέγχουμε αν έχουν επιλεχθεί και οι δύο τιμές
      if (this.startDate && this.endDate) {
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        // Έλεγχος: αν η αρχική ημερομηνία είναι μετά την τελική, εμφανίζουμε μήνυμα σφάλματος
        if (start > end) {
          alert("Error: The start date must be before the end date.");
          return;
        }
        // Αν οι ημερομηνίες είναι έγκυρες, εκπέμπουμε το event με το διάστημα [start, end]
        this.$emit('filter-change', [start, end]);
      } else {
        alert("Please select both start and end dates.");
      }
    },
    clearFilter() {
      // Καθαρίζουμε τα πεδία και εκπέμπουμε ένα κενό φίλτρο για επαναφορά των δεδομένων
      this.startDate = '';
      this.endDate = '';
      this.$emit('filter-change', []);
    }
  }
}
</script>
