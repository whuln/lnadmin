<template>
  <div v-bind:result="result">
    <Input v-model="value" @change="onChange" size="small">
       <template slot="prepend" v-if="label != null">{{label}}</template>
    </Input>
  </div>
</template>
<script>
export default {
  data () {
    return {
      value: ''
    }
  },
  model: {
    prop: 'result',
    event: 'change'
  },
  props: { label: String, result: Array },
  beforeMount () {
    this.$on('change', val => {
      return val
    })

    this.value = this.result.join(', ')
  },
  methods: {
    onChange (val) {
      this.$emit('change', val.split(','))
    }
  },
  watch: {
    result (val, oldVal) {
      this.value = val.join(', ')
    }
  }
}
</script>
