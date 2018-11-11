<template>
  <tr>
    <th scope="row">{{ position }}</th>
    <td>{{ row.name }}</td>
    <td>{{ row.monthlyCost | toCurrency}}</td>
    <td>{{ row.yearlyCost | toCurrency}}</td>
  </tr>
</template>

<script>
export default {
  data () {
    return {
    }
  },
  props: ['position', 'row']
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
