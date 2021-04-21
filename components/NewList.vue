<template>
  <view class="goodList">
    <view
      @click="routerGo(item)"
      class="item acea-row row-between-wrapper"
      v-for="(item, goodlistIndex) in newList"
      :key="goodlistIndex"
    >
      <view class="pictrue">
        <image :src="item.image" class="image" mode="aspectFill" />
        <image :src="$VUE_APP_RESOURCES_URL+'/images/one.png'" class="numPic" v-if="isSort === true && index === 0" ></image>
        <image :src="$VUE_APP_RESOURCES_URL+'/images/two.png'" class="numPic" v-if="isSort === true && index === 1" ></image>
        <image :src="$VUE_APP_RESOURCES_URL+'/images/three.png'" class="numPic" v-if="isSort === true && index === 2" ></image>
		<image src="../../../static/imgs/miao.png" v-if="item.isSeckill==1" class="imgMiao" mode=""></image>
      </view>
      <view class="underline">
        <view class="text">
          <view class="line1">{{ item.storeName }}</view>
          <span class="linebox"><image src="../static/imgs/jin.png" class="imgw" ></image>可用补贴券{{ item.otPrice - item.price }}</span>
          <view class="money font-color-red">
			  <view class="vdis">
				<i>￥</i>
				<span class="num">{{ item.price }}</span>
				<i class="mar">到手价</i>
			  </view>
				
			<text class="ftext">原价￥{{ item.otPrice || 0 }}</text>
          </view>
          <!-- <view class="vip-money acea-row row-middle">
            <view class="vip">
              原价￥{{ item.otPrice || 0
              }}
            </view>
            <span class="num">月销{{ item.ficti }}{{ item.unitName }}</span>
          </view> -->
        </view>
      </view>
      <!-- <view class="iconfont icon-gouwuche cart-color acea-row row-center-wrapper"></view> -->
    </view>
  </view>
</template>
<script>
export default {
  name: "NewList",
  props: {
    newList: {
      type: Array,
      default: () => []
    },
    isSort: {
      type: Boolean,
      default: true
    }
  },
  data: function() {
    return {};
  },
  methods:{
  	  routerGo(item) {
  	  	if(item.isSeckill&&item.isSeckill==1){
  	  		this.$yrouter.push({
  	  		  path: "/pages/activity/SeckillDetails/index",
  	  		  query: {
  	  		    id: item.skillId,
  	  		    time: item.seckillEndDate,
  	  		    msTatus: 1
  	  		  }
  	  		});
  	  	}else if(item.isPink&&item.isPink==1){
  	  			this.$yrouter.push({
  	  			  path: "/pages/activity/GroupDetails/index",
  	  			  query: { id:item.pinkId }
  	  			});
  	  		}else{
  	  			this.$yrouter.push({ path: '/pages/shop/GoodsCon/index', query: { id: item.id } });
  	  	}
  	  }
  }
};
</script>
<style scoped>
  .goodList{
    display: flex;
    justify-content: space-between;
  	flex-wrap: wrap;
  
  }
  .goodList .item{
    width: 310rpx!important;
    padding-left: 0!important;
    margin-bottom: 20rpx;
  	background-color: none!important;
  }
  .index .wrapper .goodList{
    display: flex;
    flex-wrap: wrap;
  }
  .goodList .item .pictrue {
    width: 310rpx!important;
    height: 256rpx!important;
    position: relative;
  
  }
  .goodList .item .underline{
    width: 270rpx!important;
    padding: 20rpx!important;
    border-bottom: 0!important;
    background: #f8f8f8;
    border-radius: 0 0 6px 6px!important;
  }
  .goodList .item .pictrue .image{
    border-radius: 6px 6px 0 0!important;
  }
  .goodList .item .text{
    width: 270rpx!important;
  }
  .line1{
    font-size: 28rpx!important;
  }
  .font-color-red {
    color: #D30202!important;
    display: flex;
    align-items: center;
    justify-content: space-between;
  	margin-top: 10rpx;
  }
  .ftext{
  	  font-size: 18rpx!important;
  	  color: #999999;
  	  margin-top: 6rpx;
  }
  .line1{
  	  margin-bottom: 10rpx;
  }
  .vdis{
  	  display: flex;
  	  align-items: center;
  }
  .goodList .item .text .money .num {
    font-size: 34rpx!important;
  }
  	
  .font-color-red i{
    font-size: 18rpx;
  		margin-top: 6rpx;
  }
  .mar{
    margin-left: 6rpx;
  }
  .goodList .item .text .vip-money .vip{
    font-weight: normal;
    color: #999;
    font-size: 22rpx!important;
    margin: 0!important;
  }
  .goodList .item .text .vip-money{
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 0!important;
  }
  .goodList .item .text .vip-money .num{
    margin: 0!important;
  	color: #999!important;
  }
  .goodList .item .iconfont{
    right: 0!important;
  }
  .linebox{
    background:linear-gradient(90deg,rgba(255,99,12,1) 0%,rgba(211,2,2,1) 100%);
    border-radius:10px;
    /* width: 270rpx; */
    font-size: 22rpx;
    color: #fff;
    /* display: flex; */
    margin-top: 10rpx;
  	padding: 4rpx 16rpx 4rpx 46rpx;
  	position: relative;
  }
  .imgw{
    width: 52rpx;
    height: 56rpx;
  	position: absolute;
  	left: 0;
  	top: -16rpx;
  }
  .imgMiao{
  	  width: 112rpx!important;
  	  height: 114rpx!important;
  	  position: absolute;
  	  left: -8rpx;
  	  top: -8rpx;
  }
</style>
