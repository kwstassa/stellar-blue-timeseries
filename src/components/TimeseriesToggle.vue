<template>
  <div class="mb-3" style="display: flex; justify-content: center;">
    <div v-for="series in seriesNames" :key="series" class="form-check form-check-inline">
      <!-- Το checkbox για κάθε σειρά -->
      <input
        type="checkbox"
        :id="series"
        :value="series"
        v-model="localVisible"
        @change="handleChange"
        class="form-check-input"
      />
      <!-- Το label για το checkbox -->
      <label class="form-check-label" :for="series">
        {{ series }}
      </label>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TimeseriesToggle', // Όνομα του component
  props: {
    // Πίνακας με τα ονόματα των σειρών για τα checkboxes
    seriesNames: {
      type: Array,
      required: true
    },
    // Αρχικά επιλεγμένες σειρές (checkboxes που είναι checked)
    initialVisible: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      // Δημιουργούμε ένα τοπικό αντίγραφο του initialVisible για να διαχειριζόμαστε τις αλλαγές
      localVisible: [...this.initialVisible]
    }
  },
  methods: {
    // Η μέθοδος αυτή καλείται κάθε φορά που αλλάζει κάποιο checkbox
    handleChange() {
      // Εκπέμπουμε ένα event 'visibility-change' και περνάμε το ενημερωμένο localVisible array στον parent component
      this.$emit('visibility-change', this.localVisible)
    }
  }
}
</script>

<style scoped>
/* Μπορείς να προσθέσεις επιπλέον styling εδώ αν χρειάζεται */
</style>
