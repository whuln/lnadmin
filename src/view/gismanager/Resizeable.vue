<template>
  
</template>
<script>
export default {
  name:'Resizeable',
  data(){
    return {
      timer:true
    }
  },
  methods:{
    resize(){
      // alert('resize')
    }
  },
  mounted(){
    const that = this;    

    window.onresize = () => {
      // return (() => {
      //     window.screenWidth = document.body.clientWidth
      //     that.screenWidth = window.screenWidth
      // })()
      if (this.timer) {
        this.timer = false;
        setTimeout(function() {

          that.resize()

          that.timer = true;
        }, 400);
      }
    };

  }
}
</script>
