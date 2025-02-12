<template>
  <!-- Κάρτα (card) για εμφάνιση του διαγράμματος (chart) -->
  <div class="card">
    <div class="card-body">
      <!-- Το canvas στοιχείο όπου θα σχεδιαστεί το διάγραμμα.
           Χρησιμοποιούμε το ref 'chartCanvas' για να έχουμε πρόσβαση σε αυτόν στο script -->
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script>
// Εισάγουμε τις απαραίτητες μεθόδους από το Vue
import { ref, onMounted, watch } from 'vue'
// Εισάγουμε το Chart.js και όλα τα απαραίτητα plugins/εγγεγραμμένα στοιχεία του
import { Chart, registerables } from 'chart.js'
// Εισάγουμε το dayjs για μορφοποίηση των ημερομηνιών
import dayjs from 'dayjs'

// Καταχωρούμε όλα τα registerables του Chart.js για να δουλέψει σωστά
Chart.register(...registerables)

export default {
  // Ονομάζουμε το component ως 'LineChart'
  name: 'LineChart',
  // Ορίζουμε τα props που δέχεται το component
  props: {
    // Ένα array με τα δεδομένα που θα εμφανιστούν στο διάγραμμα
    data: {
      type: Array,
      required: true
    },
    // Ένα array με τα ονόματα των σειρών (π.χ. οι μετρήσεις που εμφανίζονται ως γραμμές)
    seriesNames: {
      type: Array,
      required: true
    }
  },
  // Χρησιμοποιούμε το Composition API μέσω της setup function
  setup(props) {
    // Δημιουργούμε μια αναφορά για το canvas στοιχείο που θα χρησιμοποιηθεί από το Chart.js
    const chartCanvas = ref(null)
    // Μεταβλητή για να αποθηκεύσουμε το instance του Chart, αρχικά δεν υπάρχει (null)
    let chartInstance = null

    // Συνάρτηση για τη δημιουργία του διαγράμματος (chart)
    const createChart = () => {
      // Δημιουργούμε τα labels για τον οριζόντιο άξονα (x-axis) χρησιμοποιώντας τις ημερομηνίες
      const labels = props.data.map(row =>
        // Μορφοποιούμε την ημερομηνία σε μορφή 'DD-MM-YYYY HH:mm'
        dayjs(row.DateTime).format('DD-MM-YYYY HH:mm')
      )

      // Δημιουργούμε τα datasets για κάθε σειρά δεδομένων που έχει το chart
      const datasets = props.seriesNames.map((series, index) => {
        // Ορίζουμε ένα απλό array με χρώματα για τις γραμμές
        const colors = ['red', 'blue', 'green']
        return {
          // Το όνομα της σειράς (θα εμφανιστεί ως label στο διάγραμμα)
          label: series,
          // Τα δεδομένα που ανήκουν σε αυτή τη σειρά
          data: props.data.map(row => row.values[series]),
          // Το χρώμα της γραμμής
          borderColor: colors[index % colors.length],
          // Δεν θέλουμε να γεμίσουμε το εσωτερικό της γραμμής (απλώς μια γραμμή)
          fill: false,
          // Ορίζει πόσο λείο θα είναι το σχήμα της γραμμής
          tension: 0.1
        }
      })

      // Δημιουργούμε το Chart στο canvas που έχουμε αναφέρει στο chartCanvas
      chartInstance = new Chart(chartCanvas.value, {
        // Ορίζουμε τον τύπο του διαγράμματος ως 'line' (γραμμικό διάγραμμα)
        type: 'line',
        // Τα δεδομένα του Chart
        data: {
          labels: labels,
          datasets: datasets
        },
        // Επιλογές για το διάγραμμα
        options: {
          // Το διάγραμμα θα είναι responsive (αυτόματα προσαρμόζεται στο μέγεθος της οθόνης)
          responsive: true,
          // Απενεργοποιούμε το maintainAspectRatio για να μπορούμε να ορίζουμε το ύψος
          maintainAspectRatio: false,
          scales: {
            // Ρυθμίσεις για τον οριζόντιο άξονα (x-axis)
            x: {
              display: true,
              title: {
                display: true,
                text: 'DateTime'
              }
            },
            // Ρυθμίσεις για τον κάθετο άξονα (y-axis)
            y: {
              display: true,
              title: {
                display: true,
                text: 'Price'
              }
            }
          }
        }
      })
    }

    // Συνάρτηση για την ενημέρωση του Chart όταν αλλάζουν τα δεδομένα ή τα ονόματα των σειρών
    const updateChart = () => {
      // Ελέγχουμε αν το Chart έχει ήδη δημιουργηθεί
      if (chartInstance) {
        // Ενημερώνουμε τα labels για τον x-axis με τις νέες ημερομηνίες
        chartInstance.data.labels = props.data.map(row =>
          dayjs(row.DateTime).format('DD-MM-YYYY HH:mm')
        )
        // Ενημερώνουμε τα datasets για κάθε σειρά με τα νέα δεδομένα
        chartInstance.data.datasets = props.seriesNames.map((series, index) => {
          // Ορίζουμε ξανά τα χρώματα για τις γραμμές
          const colors = ['red', 'blue', 'green']
          return {
            label: series,
            data: props.data.map(row => row.values[series]),
            borderColor: colors[index % colors.length],
            fill: false,
            tension: 0.1
          }
        })
        // Καλούμε την update() για να ανανεωθεί το Chart και να εμφανίσει τις αλλαγές
        chartInstance.update()
      }
    }

    // Όταν το component τοποθετηθεί (mounted) στο DOM, δημιουργούμε το διάγραμμα
    onMounted(() => {
      createChart()
    })

    // Παρακολουθούμε (watch) για αλλαγές στο prop 'data'
    watch(() => props.data, () => {
      updateChart()
    }, { deep: true })

    // Παρακολουθούμε (watch) για αλλαγές στο prop 'seriesNames'
    watch(() => props.seriesNames, () => {
      updateChart()
    }, { deep: true })

    // Επιστρέφουμε στο template την αναφορά του canvas ώστε να χρησιμοποιηθεί στο <canvas>
    return {
      chartCanvas
    }
  }
}
</script>

<style scoped>
/* Στυλ για την κάρτα που περιέχει το διάγραμμα */
.card {
  margin-bottom: 20px;
}

/* Στυλ για το σώμα της κάρτας όπου εμφανίζεται το διάγραμμα.
   Ορίζουμε το ύψος σε 600px, το οποίο μπορείτε να προσαρμόσετε ανάλογα με τις ανάγκες σας */
.card-body {
  height: 600px;
}
</style>

