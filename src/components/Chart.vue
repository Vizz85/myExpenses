<script>
import { Pie, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins

export default {
  extends: Pie,
  mixins: [reactiveProp],
  data () {
    return {
      options: {
        tooltips: {
          callbacks: {
            // title: function(tooltipItem, data) {
            //   return data['labels'][tooltipItem[0]['index']];
            // },
            // label: function(tooltipItem, data) {
            //   return data['datasets'][0]['data'][tooltipItem['index']];
            // },
            afterLabel: function (tooltipItem, data) {
              const dataset = data['datasets'][0]
              const total = dataset.data.reduce((sum, value) => sum + value, 0)
              const percent = Math.round((dataset['data'][tooltipItem['index']] / total) * 100)
              return '(' + percent + '%)'
            }
          }
        }
      }
    }
  },
  mounted () {
    // this.chartData is created in the mixin.
    // If you want to pass options please create a local options object
    this.renderChart(this.chartData, this.options)
  }
}
</script>

<style>
</style>
