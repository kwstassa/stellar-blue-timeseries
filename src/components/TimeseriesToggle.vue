<template>
  <div class="mb-3" style="
    display: flex;
    justify-content: center;">
    <div v-for="series in seriesNames" :key="series" class="form-check form-check-inline">
      <input 
        type="checkbox"
        :id="series"
        :value="series"
        v-model="localVisible"
        @change="onChange"
        class="form-check-input"
      />
      <label class="form-check-label" :for="series">{{ series }}</label>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TimeseriesToggle',
  props: {
    seriesNames: {
      type: Array,
      required: true
    },
    initialVisible: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      localVisible: [...this.initialVisible]
    }
  },
  methods: {
    onChange() {
      // Emit the updated visible series array.
      this.$emit('visibility-change', this.localVisible)
    }
  }
}
</script>
