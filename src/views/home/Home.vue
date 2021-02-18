<template>
    <div id="home">
      <nav-bar class="home-nav">
        <div slot="center">购物街</div>
      </nav-bar>
      <tab-control :titles="['流行','新款','精选']"
                   class="tab-control1"
                   @tabClick="tabClick" ref="tabControl1"
                   v-show="isTabFixed"></tab-control>
      <scroll class="content" ref="scroll"
              :probe-type="3" @scroll="contentScroll"
              :pull-up-load="{threshold:-40}" @pullingUp="loadMore">
        <HomeSwiper :banners="banners" @swiperImageLoad="swiperImageLoad"></HomeSwiper>
        <RecommendView :recommends="recommends"></RecommendView>
        <feature-view></feature-view>
        <tab-control :titles="['流行','新款','精选']"
                     class="tab-control"
                     @tabClick="tabClick" ref="tabControl2"></tab-control>
        <goods-list :goods="goods[currentType].list"></goods-list>
      </scroll>
        <back-top @click.native ="backClick" v-show="isShowBackTop"></back-top>
    </div>
</template>

<script>
import HomeSwiper from "@/views/home/childComps/HomeSwiper";
import RecommendView from "@/views/home/childComps/RecommendView";
import FeatureView from "@/views/home/childComps/FeatureView";

import NavBar from "@/components/common/navbar/NavBar";
import TabControl from "@/components/content/tabControl/TabControl";
import GoodsList from "@/components/content/goods/GoodsList";
import BackTop from "@/components/content/backTop/BackTop";
import {getHomeMutidata,getHomeGoods} from "@/network/home";

import Scroll from "@/components/common/scroll/Scroll";

export default {
  name: "Home",
  components:{
    HomeSwiper,
    RecommendView,
    FeatureView,
    NavBar,
    TabControl,
    GoodsList,
    Scroll,
    BackTop
  },
  data(){
    return{
      banners:[],
      recommends:[],
      goods:{
        'pop':{page:0,list:[]},
        'new':{page:0,list:[]},
        'sell':{page:0,list:[]},
      },
      currentType:'pop',
      isShowBackTop:false,
      tabOffsetTop:0,
      isTabFixed:false
    }
  },
  created() {
    //请求多个数据
    this.getHomeMutidata();
    this.getHomeGoods('pop');
    this.getHomeGoods('new');
    this.getHomeGoods('sell');
    // getHomeGoods('pop',1).then( res =>{
    //   console.log(res)
    // })
    // this.$bus.$on('itemImageLoad',() => {
    //   this.$refs.scroll.refresh()
    // })
  },
  methods:{
    //事件监听
    tabClick(index){
      // console.log(index)

      switch (index){
        case 0:
          this.currentType='pop'
              break
        case 1:
          this.currentType='new'
              break
        case 2:
          this.currentType='sell'
              break
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
      // console.log(this.currentType)
    },
    backClick(){
      this.$refs.scroll.scrollTo(0,0,500)
    },
    contentScroll(position){
      //console.log(position);
      if(position.y < -1000){
        this.isShowBackTop = true
      }else{
        this.isShowBackTop = false
      }
      this.isTabFixed = (-position.y) > this.tabOffsetTop
    },
    loadMore(){
      this.getHomeGoods(this.currentType)
    },
    swiperImageLoad(){
      this.tabOffsetTop=this.$refs.tabControl2.$el.offsetTop;
    },


    //网络请求部分
    getHomeMutidata(){
      getHomeMutidata().then(res => {

        this.banners=res.data.banner.list;
        this.recommends=res.data.recommend.list;
      })
    },
    getHomeGoods(type){
      const page=this.goods[type].page + 1
      getHomeGoods(type,page).then( res => {
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1
        //完成上拉加载
        this.$refs.scroll.finishPullUp()
      })
    }
  }
}
</script>

<style scoped>
#home{
  /*padding-top: 44px;*/
  height: 100vh;
  position: relative;
}
.home-nav{
  background-color: var(--color-tint);
  color: white;
  /*position: fixed;*/
  /*left: 0;*/
  /*right: 0;*/
  /*top:0;*/
  /*z-index: 8;*/
}
.tab-control1{
  position: relative;
  z-index: 8;
}
/*.tab-control{*/
/*  position: sticky;*/
/*  top:44px;*/
/*  z-index: 9;*/
/*}*/
.content{
  position: absolute;
  top:44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
.content{
  height: calc(100% - 93px);
  overflow: hidden;

}

</style>
