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
      <input type="text" class="form-control" placeholder="inserisci voce" aria-label="Cost description" v-model="costDescription">
      <input type="number" class="form-control" placeholder="inserisci costo" aria-label="Cost" v-model="cost">
      <div class="input-group-append">
        <button class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">cadenza</button>
        <div class="dropdown-menu">
          <a class="dropdown-item" href="#" @click="addRow('montly')">mensile</a>
          <a class="dropdown-item" href="#" @click="addRow('yearly')">annuale</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SingleRow from './components/SingleRow'

export default {
  name: 'App',
  components: {
    SingleRow
  },
  data () {
    return {
      rows: [],
      cost: '',
      costDescription: ''
    }
  },
  created () {
    this.rows = JSON.parse(window.localStorage.getItem('data'))
  },
  watch: {
    rows () {
      this.setStorage()
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
      if (!type || this.costDescription === '' || this.cost === '') {
        return
      }
      const row = {
        name: this.costDescription,
        monthlyCost: type === 'yearly' ? this.cost / 12 : Number(this.cost),
        yearlyCost: type === 'yearly' ? Number(this.cost) : this.cost * 12
      }
      this.rows.push(row)
      this.cost = ''
      this.costDescription = ''
    },
    deleteRow (position) {
      this.rows.splice(position, 1)
    },
    setStorage () {
      window.localStorage.setItem('data', JSON.stringify(this.rows))
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
}
</style>
