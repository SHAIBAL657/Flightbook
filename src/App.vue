<template>
<div>
    <comCard v-if="bool"></comCard>
    <comTable v-else></comTable>
    
</div>
</template>

<script>
import comCard from './components/Card.vue'
import comTable from './components/Table.vue'

export default {
  date(){
    bool: true
  },
  components: {
    comCard,
    comTable
  },
  props:["bool"]
}
</script>

