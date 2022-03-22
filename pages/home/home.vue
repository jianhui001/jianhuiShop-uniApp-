<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item,index) in swiperList" :key='index'>
        <!-- url路径加"/" -->
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id='+ item.goods_id" >
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    
    <!-- 分类区域 -->
    <view class="nav_list">
      <view class="nav_item" v-for="(item,index) in navList" 
:key="index" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav_img"></image>
      </view>
    </view>
    
    <!-- 楼层区域 -->
    <view class="floor_list">
      <!-- 每一个楼层的item项 -->
      <view class="floor_item" v-for="(item,index) in floorList" :key="index">
        <!-- 楼层标题 -->
        <image :src="item.floor_title.image_src" class="floor_title"></image>
        
        <!--  楼层图片区域-->
        <view class="floor-img-box">
          <!-- 图片左侧 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" mode="widthFix" 
            :style="{width:item.product_list[0].image_width+'rpx'}"></image>
          </navigator>
          <!-- 图片右侧 -->
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,index2) in item.product_list"  :key="index" v-if="index2 !== 0" :url="item2.url">
              <image :src="item2.image_src"  :style="{width:item2.image_width + 'rpx'}" mode="widthFix"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
         //  轮播图的数据列表，默认为空数组
        swiperList:[],
        navList:[],
        floorList:[]
      };
    },
   onLoad(){
     //  在小程序页面刚加载的时候，调用获取轮播图数据的方法
     this.getSwiperList()
     this.getNavList()
     this.getFloorList()
   },
    methods:{
      //  获取轮播图数据的方法
     async getSwiperList(){
        const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata') 
        if (res.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.message
      },
       //  获取导航分类的方法
    async getNavList() {
      const { data: res } = await uni.$http.get('/api/public/v1/home/catitems')
      if (res.meta.status !== 200) return uni.$showMsg()
      this.navList = res.message
    },
    // 跳转到分类页面
    navClickHandler(item){
      if(item.name === '分类')
      uni.switchTab({
         url: '/pages/cate/cate'
      })
    },
    //  获取楼层数据列表的方法
    async getFloorList(){
      const {data: res} = await uni.$http.get('/api/public/v1/home/floordata')
      if(res.meta.status !== 200) return uni.$showMsg()
      
      // 通过双层 forEach 循环，处理 URL 地址
      res.message.forEach(floor =>{
        floor.product_list.forEach(prod => {
          prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
        })
      })
        
      this.floorList = res.message
    }
    }
  }
</script>

<style lang="scss">
 swiper{
   height: 330rpx;
   .swiper-item, image{
     width: 100%;
     height: 100%;
   }
 }
 
 .nav_list{
   display: flex;
   justify-content: space-around;
   margin: 15px 0;
   .nav_img{
     width: 128rpx;
     height: 140rpx;
   }
 }
 
 .floor_list{
   .floor_title{
      height: 60rpx;
      width: 100%;
       // display: flex;
   }
 }
 
 .right-img-box {
   display: flex;
   flex-wrap: wrap;
   justify-content: space-around;
 }
 
 .floor-img-box{
   display: flex;
   padding-left: 10rpx;
 }
</style>
