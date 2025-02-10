<template>
  <div>
    <table class="table table-sm table-bordered table-striped table-hover">
      <thead class="table-dark">
        <tr>
          <th scope="col">DateTime</th>
          <th scope="col" v-for="series in seriesNames" :key="series">{{ series }}</th>
        </tr>
      </thead>
      <tbody>
        <!-- Use the computed "paginatedData" to display only the rows for the current page -->
        <tr v-for="(row, rowIndex) in paginatedData" :key="row.DateTime">
          <td>{{ formatDate(row.DateTime) }}</td>
          <td v-for="series in seriesNames" :key="series">
            <input
              type="text"
              :value="row.values[series]"
              class="form-control"
              @change="onInputChange($event, rowIndex, series)"
            />
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Pagination controls -->
    <div class="d-flex justify-content-between align-items-center mt-2">
      <!-- Selection for number of entries to display -->
      <div>
        <label for="entriesSelect" class="me-2">Show entries:</label>
        <select
          id="entriesSelect"
          class="form-select d-inline-block"
          style="width: auto;"
          v-model.number="entriesPerPage"
          @change="resetPage"
        >
          <option :value="10">10</option>
          <option :value="20">20</option>
          <option :value="30">30</option>
          <!-- "0" will represent "All entries" -->
          <option :value="0">All entries</option>
        </select>
      </div>

      <!-- Next/Previous buttons (only applicable when a specific number is selected) -->
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
  </div>
</template>

<script>
import dayjs from 'dayjs'

export default {
  name: 'DataTable',
  props: {
    // The complete set of data rows passed from the parent component
    data: {
      type: Array,
      required: true
    },
    seriesNames: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      // Pagination state: current page and the number of entries per page.
      // Default: currentPage = 1, entriesPerPage = 10.
      currentPage: 1,
      entriesPerPage: 10 // If set to 0, display all entries.
    }
  },
  computed: {
    // Compute the paginated data to display based on the current page and entriesPerPage.
    paginatedData() {
      if (this.entriesPerPage === 0) {
        // "All entries" selected – return all data.
        return this.data;
      } else {
        const start = (this.currentPage - 1) * this.entriesPerPage;
        return this.data.slice(start, start + this.entriesPerPage);
      }
    },
    // Calculate the total number of pages. If "All entries" is selected, totalPages is 1.
    totalPages() {
      if (this.entriesPerPage === 0) return 1;
      return Math.ceil(this.data.length / this.entriesPerPage);
    }
  },
  methods: {
    // Format the DateTime string using Day.js.
    formatDate(dateString) {
      return dayjs(dateString).format('DD-MM-YYYY HH:mm');
    },
    // Handle changes to the input fields in the table.
    onInputChange(e, rowIndex, series) {
      const newValue = e.target.value;
      const parsedValue = parseFloat(newValue);
      if (isNaN(parsedValue) || parsedValue < -2000 || parsedValue > 2000) {
        alert("Invalid input. Please enter a number between -2000 and 2000.");
        e.target.value = this.data[rowIndex].values[series];
      } else {
        // Emit an event to update the cell in the parent component.
        this.$emit('update-cell', { rowIndex, series, value: parsedValue });
      }
    },
    // Reset the current page to 1 whenever the user changes the entries-per-page selection.
    resetPage() {
      this.currentPage = 1;
    },
    // Go to the previous page (if not already on the first page).
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    // Go to the next page (if not already on the last page).
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    }
  }
}
</script>

<style scoped>
/* You can add any additional styling here if needed */
</style>

