<template>
  <div id="app" class="container">
    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Voce</th>
          <th scope="col">spesa mensile</th>
          <th scope="col">spesa annuale</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <SingleRow v-for="(row, idx) in rows" :key="idx" :position="idx" :row="row" @deleteRow="deleteRow"/>
      </tbody>
      <tfoot>
        <tr>
          <th scope="col"></th>
          <th scope="col">Totale</th>
          <th scope="col">{{ monthlySum | toCurrency }}</th>
          <th scope="col">{{ yearlySum | toCurrency }}</th>
          <th></th>
        </tr>
      </tfoot>
    </table>

    <div class="input-group">
      <input type="text" class="form-control" placeholder="inserisci voce" aria-label="Cost description" v-model="description">
      <input type="text" class="form-control" placeholder="inserisci categoria" aria-label="Cost category" v-model="category">
      <input type="number" class="form-control" placeholder="inserisci costo" aria-label="Cost" v-model="cost">
      <div class="input-group-append">
        <button class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">cadenza</button>
        <div class="dropdown-menu">
          <a class="dropdown-item" href="#" @click="addRow('montly')">mensile</a>
          <a class="dropdown-item" href="#" @click="addRow('yearly')">annuale</a>
        </div>
      </div>
    </div>

    <pie-chart :chart-data="datacollection"></pie-chart>
  </div>
</template>

<script>
import SingleRow from './components/SingleRow'
import PieChart from './components/Chart'

export default {
  name: 'App',
  components: {
    SingleRow,
    PieChart
  },
  data () {
    return {
      rows: JSON.parse(window.localStorage.getItem('data')) || [],
      cost: '',
      description: '',
      category: '',
      datacollection: null
    }
  },
  mounted () {
    this.fillData()
  },
  watch: {
    rows () {
      this.setStorage()
      this.fillData()
    }
  },
  computed: {
    monthlySum () {
      return this.rows.reduce((acc, row) => acc + row.monthlyCost, 0)
    },
    yearlySum () {
      return this.rows.reduce((acc, row) => acc + row.yearlyCost, 0)
    }
  },
  methods: {
    addRow (type) {
      if (!type || this.description === '' || this.cost === '') {
        return
      }
      const row = {
        name: this.description,
        category: this.category,
        monthlyCost: type === 'yearly' ? this.cost / 12 : Number(this.cost),
        yearlyCost: type === 'yearly' ? Number(this.cost) : this.cost * 12
      }
      this.rows.push(row)
      this.cost = ''
      this.description = ''
      this.category = ''
    },
    deleteRow (position) {
      this.rows.splice(position, 1)
    },
    setStorage () {
      window.localStorage.setItem('data', JSON.stringify(this.rows))
    },
    fillData () {
      const labels = [...new Set(this.rows.map(row => row.category || 'other'))]
      const data = []
      labels.forEach(label => {
        let sum = 0
        this.rows.forEach(row => {
          const category = row.category || 'other'
          if (category === label) sum += row.yearlyCost
        })
        data.push(sum)
      })
      this.datacollection = {
        labels,
        datasets: [{
          data,
          backgroundColor: ['#3366CC', '#DC3912', '#FF9900', '#109618', '#990099', '#3B3EAC', '#0099C6', '#DD4477', '#66AA00', '#B82E2E', '#316395', '#994499', '#22AA99', '#AAAA11', '#6633CC', '#E67300', '#8B0707', '#329262', '#5574A6', '#3B3EAC']
        }]
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-bottom: 60px;
}
</style>
