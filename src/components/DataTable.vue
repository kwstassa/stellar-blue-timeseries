<template>
  <table class="table table-sm table-bordered table-striped table-hover">
    <thead class="table-dark">
      <tr>
        <th scope="col">DateTime</th>
        <!-- Apply the price-col class to each price column header -->
        <th scope="col" v-for="series in seriesNames" :key="series" class="price-col">
          {{ series }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, rowIndex) in data" :key="row.DateTime">
        <td>{{ formatDate(row.DateTime) }}</td>
        <!-- Apply the price-col class to each price column cell -->
        <td v-for="series in seriesNames" :key="series" class="price-col">
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
</template>

<script>
import dayjs from 'dayjs'

export default {
  name: 'DataTable',
  props: {
    data: {
      type: Array,
      required: true
    },
    seriesNames: {
      type: Array,
      required: true
    }
  },
  methods: {
    formatDate(dateString) {
      // Format as "DD-MM-YYYY HH:mm"
      return dayjs(dateString).format('DD-MM-YYYY HH:mm')
    },
    onInputChange(e, rowIndex, series) {
      const newValue = e.target.value
      // Validate: must be a number between -2000 and 2000.
      const parsedValue = parseFloat(newValue)
      if (isNaN(parsedValue) || parsedValue < -2000 || parsedValue > 2000) {
        alert("Invalid input. Please enter a number between -2000 and 2000.")
        // Reset to the original value.
        e.target.value = this.data[rowIndex].values[series]
      } else {
        // Emit updated value.
        this.$emit('update-cell', { rowIndex, series, value: parsedValue })
      }
    }
  }
}
</script>

<style scoped>
/* Adjust the width as needed to make the price columns thinner */
.price-col {
  width: 80px;
}
</style>
