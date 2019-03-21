<template>
    <div>
        图层资源管理
        {{test}}<br>
        a={{a}}<br>
        {{testgetter}}<br>
         <Button @click="testmutation">testmutation</Button>
         <Button @click="testaction">testaction</Button>
    </div>
</template>
<script>
import { createNamespacedHelpers } from 'vuex'
const { mapState, mapMutations, mapActions, mapGetters } = createNamespacedHelpers('gis')
export default {
  name: 'MaplayerManage',
  computed: {
    ...mapState({
      a: state => state.m1.m1test
    }),
    ...mapGetters([
      'testgetter'
    ]),
    test () {
      return this.$store.state.gis.test
    }
  },
  methods: {
    ...mapMutations([
      'testmutation'
    ]),
    ...mapActions([
      'testaction'

    ])
  }
}
</script>
