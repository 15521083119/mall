<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">蘑菇街</div>
    </nav-bar>
    <home-swiper :banners="banners" />
    <recommend-view :recommends="recommends" />
    <feature-view />
    <tab-control :titles="['流行','新款','精选']" class="tab-control" @tabClick="tabClick"/>
    <good-list :goods="showGods"/>
    
  </div>
</template>

<script>
import NavBar from "components/common/navbar/NavBar";
import TabControl from "components/content/tabControl/TabControl";
import GoodList from 'components/content/goods/GoodList'

import HomeSwiper from "./childComps/HomeSwiper";
import RecommendView from "./childComps/RecommendView";
import FeatureView from "./childComps/FeatureView";

import { getHomeMultidata, getHomeGods } from "network/home";

export default {
  name: "Home",
  components: {
    NavBar,
    TabControl,
    GoodList,
    HomeSwiper,
    RecommendView,
    FeatureView
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] }
      },
      currentType: 'pop'
    };
  },
  created() {
    this.getHomeMultidata();
    this.getHomeGods('pop');
    this.getHomeGods('new');
    this.getHomeGods('sell');
  },
  computed: {
    showGods() {
      return this.goods[this.currentType].list
    }
  },
  methods: {
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    getHomeGods(type) {
      const page = this.goods[type].page + 1 
      getHomeGods(type, page).then(res => {
        this.goods[type].list.push(...res.data.list)
        this.goods[type].page += 1 
      });
    },

    //********************************************* */
    tabClick(index){
      switch(index){
        case 0:
          this.currentType = 'pop'
          break
        case 1:
          this.currentType = 'new'
          break
        case 2:
          this.currentType = 'sell'
          break
      }
    }
  }
};
</script>

<style scoped>
#home {
  padding-top: 44px;
  padding-bottom: 49px;
}
.home-nav {
  background-color: var(--color-tint);
  color: #ffffff;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  z-index: 9;
}
.tab-control {
  position: sticky;
  top: 44px;
  z-index: 9;
}
</style>