<template>
  <div id="app" class="container-fluid">
    <h1 class="text-center m-4">Timeseries Data Visualization</h1>
    
    <!-- Date Filter and Timeseries Toggle are placed on top -->
    <DateFilter @filter-change="onFilterChange" />
    <TimeseriesToggle 
      :seriesNames="allSeries" 
      :initialVisible="visibleSeries"
      @visibility-change="updateVisibleSeries"
    />

    <!-- Row containing the table and chart side by side with extra gap -->
    <div class="row ">
      
      <!-- Line Chart Column -->
      <div class="col-12 col-xxl-6">
        <LineChart 
          :data="filteredData" 
          :seriesNames="visibleSeries" 
        />
      </div>
      <!-- Data Table Column -->
      <div class="col-xxl-6">
        <DataTable 
          :data="filteredData" 
          :seriesNames="visibleSeries" 
          @update-cell="onCellUpdate"
        />
      </div>
    </div>
  </div>
</template>

<script>
import dayjs from 'dayjs'
import DataTable from './components/DataTable.vue'
import LineChart from './components/LineChart.vue'
import DateFilter from './components/DateFilter.vue'
import TimeseriesToggle from './components/TimeseriesToggle.vue'
import dataJson from './assets/data.json'

export default {
  name: 'App',
  components: {
    DataTable,
    LineChart,
    DateFilter,
    TimeseriesToggle
  },
  data() {
    return {
      rawData: [],
      filteredData: [],
      allSeries: ['ENTSOE_DE_DAM_Price', 'ENTSOE_GR_DAM_Price', 'ENTSOE_FR_DAM_Price'],
      visibleSeries: ['ENTSOE_DE_DAM_Price', 'ENTSOE_GR_DAM_Price', 'ENTSOE_FR_DAM_Price'],
      filterRange: []
    }
  },
  created() {
    this.initializeData(dataJson)
  },
  methods: {
    initializeData(data) {
      // Convert the JSON numeric strings to numbers.
      this.rawData = data.map(item => ({
        DateTime: item.DateTime,
        ENTSOE_DE_DAM_Price: parseFloat(item.ENTSOE_DE_DAM_Price),
        ENTSOE_GR_DAM_Price: parseFloat(item.ENTSOE_GR_DAM_Price),
        ENTSOE_FR_DAM_Price: parseFloat(item.ENTSOE_FR_DAM_Price)
      }))
      // Initially, filteredData shows all data.
      this.filteredData = this.rawData.map(item => ({
        DateTime: item.DateTime,
        values: {
          ENTSOE_DE_DAM_Price: item.ENTSOE_DE_DAM_Price,
          ENTSOE_GR_DAM_Price: item.ENTSOE_GR_DAM_Price,
          ENTSOE_FR_DAM_Price: item.ENTSOE_FR_DAM_Price
        }
      }))
    },
    onFilterChange(dateRange) {
  this.filterRange = dateRange;

  if (dateRange && dateRange.length === 2) {
    // Convert input values to dayjs objects directly.
    const start = dayjs(dateRange[0]);
    const end = dayjs(dateRange[1]);

    // Log the filter boundaries for debugging.
    console.log(
      "Applying filter from",
      start.format("YYYY-MM-DD HH:mm:ss"),
      "to",
      end.format("YYYY-MM-DD HH:mm:ss")
    );

    this.filteredData = this.rawData
      .filter(item => {
        const dt = dayjs(item.DateTime);
        // Log each row’s DateTime for debugging.
        console.log("Row date:", dt.format("YYYY-MM-DD HH:mm:ss"));
        // Include rows where dt is not before start and not after end.
        return !dt.isBefore(start) && !dt.isAfter(end);
      })
      .map(item => ({
        DateTime: item.DateTime,
        values: {
          ENTSOE_DE_DAM_Price: item.ENTSOE_DE_DAM_Price,
          ENTSOE_GR_DAM_Price: item.ENTSOE_GR_DAM_Price,
          ENTSOE_FR_DAM_Price: item.ENTSOE_FR_DAM_Price
        }
      }));
  } else {
    // If no valid filter, show all data.
    this.filteredData = this.rawData.map(item => ({
      DateTime: item.DateTime,
      values: {
        ENTSOE_DE_DAM_Price: item.ENTSOE_DE_DAM_Price,
        ENTSOE_GR_DAM_Price: item.ENTSOE_GR_DAM_Price,
        ENTSOE_FR_DAM_Price: item.ENTSOE_FR_DAM_Price
      }
    }));
  }
}
,
    updateVisibleSeries(newVisible) {
      this.visibleSeries = newVisible
    },
    onCellUpdate({ rowIndex, series, value }) {
      const row = this.filteredData[rowIndex]
      if (row && row.values) {
        row.values[series] = value
        const rawIndex = this.rawData.findIndex(item => item.DateTime === row.DateTime)
        if (rawIndex !== -1) {
          this.rawData[rawIndex][series] = value
        }
      }
    }
  }
}
</script>


<style>
   body {background-color: #D3D3D3 !important;} 
  
</style>
