<template>
  <div id="homeBox">
    <div class="home_NavBar"><nav-bar></nav-bar></div>
    <tab-control v-show="tabFlexChange"  ref="tabControl2" class="tab_control2" :titles="['流行','新款','精选']" @tabClick="tabClick"  />


    <!-- <div>{{banner.image}}</div> -->
    <b-scroll ref="Bscroll" class="Bscroll" 
    :probe-type="3" 
    @scroll="scroll"
    :pull-upload="true"
    @pullingUp="pullingUp"
    >
      <home-swiper class="home_swiper" ref="HomeSwiper" :banners='banners'/>
      <home-recommend :recommends='recommends' ref="HomeRecommend" />
      <tab-control  ref="tabControl1" class="tab_control" :titles="['流行','新款','精选']" @tabClick="tabClick"  />
      <home-list :goodsList="goods[currentType].list"/>
    </b-scroll>
    <back-top @click.native="BacktopClick" v-show="backisShow" />
    
  </div>
</template>

<script>  
import NavBar from "components/common/navbar/NavBar"
import {getHomeMultidata,getHomeGoods} from "network/home"
import HomeRecommend from './childComps/HomerecomendView'
import HomeSwiper from "./childComps/HomeSwiper"
import TabControl from "components/content/tabControl/TabControl"
import HomeList from "components/content/goods/goodslist"
import BScroll from "components/content/bscroll/bscroll"
import BackTop from "components/content/backtop/backtop"
import { debounce } from "common/utils"

export default {
  data(){
    return{
      banners:null,
      recommends:null,
      goods:{
            'pop':{page:0,list:[]},
            'new':{page:0,list:[]},
            'sell':{page:0,list:[]},
        },
      currentType:"pop",
      backisShow:false,
      tabFlexChange:false

    }
  },
  components:{
    NavBar,
    HomeSwiper, 
    HomeRecommend,
    TabControl,
    HomeList,
    BScroll,
    BackTop
  },
  created(){
    this.getHomeMultidata()
    this.getHomeGoods("pop")
    this.getHomeGoods("new")
    this.getHomeGoods("sell")
    
  },
  mounted(){
    const  refresh = debounce(this.$refs.Bscroll.refresh,200)
    this.$bus.$on('HomeimgUpload',()=>{
      refresh()
    })
    
   
  },
  updated(){
    // console.log(this.$refs.HomeSwiper.$el.offsetHeight);
  },
  destroyed(){
    // console.log(666);
  },
  activated(){
    // console.log(777);
  },
  deactivated(){
    // console.log(888);

  },
  methods:{
    
    //网络请求相关
    getHomeMultidata(){
      getHomeMultidata().then(res=>{
    //  console.log(res);
     this.banners = res.data.banner.list
     this.recommends = res.data.recommend.list
    })
    },
    getHomeGoods(type){
      const page =  this.goods[type].page+1
       getHomeGoods(type,page).then(res=>{
      this.goods[type].list.push(...res.data.list)
      this.goods[type].page += 1
      this.$refs.Bscroll.finishPullUp()
      })

    },
    //事件监听-------------
    tabClick(index){
      // console.log(index);
      switch(index){
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
      console.log(this.$refs.tabControl1.currentIndex);
      console.log(this.$refs.tabControl2.currentIndex);

      // this.$refs.tabControl1.currentIndex = index
      // this.$refs.tabControl2.currentIndex = index

    },
    BacktopClick(){
      this.$refs.Bscroll.scrollTo(0,0)
    },
    scroll(position){
      // console.log(position.y);
      //回到顶部
      this.backisShow= (-position.y) > 1500
      //显示
      this.tabFlexChange= (-position.y) > 312
    },
    pullingUp(){
      // console.log(123);
      this.getHomeGoods(this.currentType)
      this.$refs.Bscroll.refresh()
      
    },
    
  }
}
</script>

<style>
.home_NavBar{
  background-color: var(--color-tint);
  color: aliceblue;
  position: fixed;
  width: 100%;
  z-index: +9;
}
.home_swiper{
  padding-top: 44px;
}
.tab_control{
  /* position: relative; */
  /* top: 44px; */
}
.Bscroll{
  height: calc(100vh - 44px);
  overflow: hidden;
  justify-content: space-around;

}
.tab_control2{
  position: absolute;
  width: 100%;
  top: 43px;
  z-index: 10;
}
</style>