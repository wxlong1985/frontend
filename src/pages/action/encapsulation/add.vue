<template>
  <div>
    <save-action :is-add="true" :is-test-case="false" />
  </div>
</template>

<script>
import SaveAction from '../components/SaveAction'

export default {
  name: 'AddEncapsulationAction',
  components: {
    SaveAction
  }
}
</script>
