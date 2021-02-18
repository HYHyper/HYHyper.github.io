<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'

export default {
  name: "Scroll",
  props:{
    probeType:{
      type:Number,
      default:0
    },
    pullUpLoad: {
      type: Object,
      default(){
        return {threshold:-20}
      }
    }
  },
  data(){
    return{
      scroll:null
    }
  },
  mounted() {
    setTimeout(() => {
      this.scroll = new BScroll(this.$refs.wrapper, {
        click: true,
        useTransition: false,
        probeType:this.probeType,
        pullUpLoad:this.pullUpLoad,
        observeDom:true,
        observeImage:true
      })
      this.scroll.scrollTo(0, 0)
      //监听滚动位置
      this.scroll.on('scroll', (position) => {
        // console.log(position);
        this.$emit('scroll', position)
      })
      //上拉加载
      this.scroll.on('pullingUp', () => {
        // console.log('上拉加载')
        this.$emit('pullingUp')
      })
    }, 20)
  },
  methods:{
    scrollTo(x,y,time=300){
      this.scroll && this.scroll.scrollTo && this.scroll.scrollTo(x,y,time)
    },
    finishPullUp() {
      this.scroll && this.scroll.finishPullUp()
    },
    // refresh(){
    //   this.scroll.refresh()
    // }


}
}
</script>

<style scoped>

</style>
