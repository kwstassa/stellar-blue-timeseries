<template>
  <!-- Flex container με κεντράρισμα, ευθυγράμμιση και gap μεταξύ των στοιχείων -->
  <div class="mb-3 d-flex flex-md-row flex-column justify-content-center ">
    <!-- Πεδίο για Start DateTime -->
    <div class="d-flex flex-column"style="margin-right:5%">
      <label for="start" class="form-label">Start DateTime:</label>
      <!-- Ορίζουμε πλάτος (π.χ. 220px) για να διατηρείται ομοιόμορφη εμφάνιση -->
      <input
        type="datetime-local"
        v-model="startDate"
        id="start"
        class="form-control"
        style="width: 220px;"
      />
    </div>
    <!-- Πεδίο για End DateTime -->
    <div class="d-flex flex-column" style="margin-right:2%; height:38px">
      <label for="end" class="form-label">End DateTime:</label>
      <input
        type="datetime-local"
        v-model="endDate"
        id="end"
        class="form-control"
        style="width: 220px;"
      />
    </div>
    <!-- Κουμπιά Apply/ Clear Filter -->
    <div class="d-flex  flex-md-row flex-column justify-content-md-center justify-content-start ">
      <button @click="applyFilter"class="btn btn-primary text-nowrap" style="margin-right:5%; margin-top: 30px;">Apply Filter</button>
      <button @click="clearFilter" class="btn btn-secondary text-nowrap" style="margin-right:5%;  margin-top: 30px;">Clear Filter</button>
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
      if (this.startDate && this.endDate) {
        const start = new Date(this.startDate);
        const end = new Date(this.endDate);
        if (start > end) {
          alert("Error: The start date must be before the end date.");
          return;
        }
        this.$emit('filter-change', [start, end]);
      } else {
        alert("Please select both start and end dates.");
      }
    },
    clearFilter() {
      this.startDate = '';
      this.endDate = '';
      this.$emit('filter-change', []);
    }
  }
}
</script>
