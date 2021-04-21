<template>
  <view ref="container"  class="pagesdwed">
	<view class="clearDataHis" v-if="collectProductList.length">
		<view class="boxInput"  @click="goGoodSearch()">
		  <text class="iconfont icon-sousuo"></text>
		  <input placeholder="搜索商品信息"  disabled />
		</view>
		<view class="imgdispl" @click="cancel">
			<image src="../../../static/imgs/dels.png" class="imgdel"></image>清空
		</view>
	</view>
	<scroll-view :scroll-y="isScroll" :style="{ height: windowHeight + 'px' }" v-if="collectProductList.length">
		<block :key="index" v-for="(items, index) in collectProductList">
			<view :data-index="index" class="order-item" @touchstart="drawStart" @touchmove="drawMove" @touchend="drawEnd" :style="{ right: items.right + 'rpx' }">
				<view class="MyTracks">
				  <view class="item acea-row row-between-wrapper" @click="goGoodsCon(items)" >
				    <view class="pictrue">
				      <image :src="items.image" />
				    </view>
				    <view class="text row-column-between">
				      <view class="infor line1">{{ items.storeName }}</view>
					  <span class="btquans">
					  	<img src="../../../static/imgs/jin.png" class="inmg" />
					  	<span>可用补贴券{{items.otPrice - items.price}}</span>
					  </span>
				      <view class="acea-row row-between-wrapper topMeds">
				        <view class="money font-color-red" style="display: flex; align-items: center;">
							￥<span style='margin-right: 6rpx;'>{{items.price}}</span>到手价
					    	<text class="yj">原价 ￥{{items.otPrice}}</text>
						</view>
				        <view class="delete" @tap.stop="goItem(items.cateId)">看相似</view>
				      </view>
				    </view>
				  </view>
				</view>
				<view class="remove" @tap.stop="delCollection(index)">删除</view>
			</view>
		</block>
	</scroll-view>
    <Loading :loaded="loadend" :loading="loading"></Loading>
    <view
      class="noCommodity"
      style="background-color:#fff;"
      v-if="collectProductList.length < 1 && page > 1"
    >
      <view class="noPictrue">
        <image src="@/static/images/noCollection.png" class="image" />
      </view>
      <Recommend></Recommend>
    </view>
	<view class="mask" v-if='mkfalse'>
		<view class="boxView">
			<view>您要清空全部收藏吗？</view>
			<view>
				<text @click="cancel">取消</text>
				<text @click="clearStorage">确定</text>
			</view>
		</view>
	</view>
  </view>
</template>
<script>
import Recommend from "@/components/Recommend";
import { getCollectUser, getCollectDel } from "@/api/user";
import Loading from "@/components/Loading";
export default {
  name: "GoodsCollection",
  components: {
    Recommend,
    Loading
  },
  props: {},
  data: function() {
    return {
      page: 1,
      limit: 20,
      collectProductList: [],
      loadTitle: "",
      loading: false,
      loadend: false,
	  mkfalse: false,
	  startX:'',
	  delBtnWidth: 160,
	  isScroll: true,
	  windowHeight: 0
    };
  },
  mounted: function() {
    this.get_user_collect_product();
  },
  onLoad: function(options) {
	 var that = this;
	 wx.getSystemInfo({
		 success: function(res) {
			 that.windowHeight = res.windowHeight;
		 }
	 });
  },
  onReachBottom() {
    !this.loading && this.get_user_collect_product();
  },
  methods: {
	  goGoodSearch() {
	  	this.$yrouter.push('/pages/shop/GoodSearch/index');
	  },
	  cancel(){
		this.mkfalse = !this.mkfalse;
	  },
	  clearStorage(){
		this.mkfalse = !this.mkfalse;
		getCollectDel(-1).then(function() {
		  uni.showToast({
		    title: "清空成功",
		    icon: "success",
		    duration: 2000,
		  })
		})
		this.collectProductList = []
	  },
	  drawStart: function(e) {
		  console.log("drawStart");
		  var touch = e.touches[0];
		  for (var index in this.collectProductList) {
			  this.collectProductList[index].right = 0;
		  }
		  this.startX = touch.clientX;
	  },
	  drawMove: function(e) {
		  var touch = e.touches[0];
		  var item = this.collectProductList[e.currentTarget.dataset.index];
		  var disX = this.startX - touch.clientX;
 
		  if (disX >= 20) {
			  if (disX > this.delBtnWidth) {
				  disX = this.delBtnWidth;
			  }
			  this.isScroll = false;
			  this.collectProductList[e.currentTarget.dataset.index].right = disX;
		  } else {
			  this.isScroll = true;
			  this.collectProductList[e.currentTarget.dataset.index].right = 0;
		  }
	  },
	  drawEnd: function(e) {
		  var item = this.collectProductList[e.currentTarget.dataset.index];
		  if (item.right >= this.delBtnWidth / 2) {
			  this.isScroll = true;
			  this.collectProductList[e.currentTarget.dataset.index].right = this.delBtnWidth;
		  } else {
			  this.isScroll = true;
			  this.collectProductList[e.currentTarget.dataset.index].right = 0;
		  }
	  },
	  goItem(id) {
		  this.$yrouter.push({
			path: "/pages/shop/GoodsList/index",
			query: { id: id}
		  });
	  },
	   goGoodsCon(item) {
	     this.$yrouter.push({
	       path: "/pages/shop/GoodsCon/index",
	       query: { id: item.id }
	     });
	   },
    get_user_collect_product: function() {
      let that = this;
      if (that.loading) return; //阻止下次请求（false可以进行请求）；
      if (that.loadend) return; //阻止结束当前请求（false可以进行请求）；
      that.loading = true;
      getCollectUser(that.page, that.limit).then(res => {
        that.loading = false;
        //apply();js将一个数组插入另一个数组;
        that.collectProductList.push.apply(that.collectProductList, res.data);
        that.loadend = res.data.length < that.limit; //判断所有数据是否加载完成；
        that.page = that.page + 1;
      });
    },
    //删除收藏；
    delCollection: function(index) {
      let that = this,
        id = that.collectProductList[index].pid,
        category = that.collectProductList[index].category;
      getCollectDel(id, category).then(function() {
        uni.showToast({
          title: "删除成功",
          icon: "success",
          duration: 2000,
          complete: () => {
            that.collectProductList.splice(index, 1);
            that.$set(that, "collectProductList", that.collectProductList);
          }
        });
      });
    }
  }
};
</script>
<style scoped>
	.boxInput{
		width: 540rpx;
		display: flex;
		align-items: center;
		background: #fff;
		border-radius: 40rpx;
		padding: 8rpx 20rpx;
		border: 1px solid #D30202;
	}
	.boxInput input {
		margin-left: 10rpx;
		font-size: 28rpx;
	}
	.pagesdwed{
		background: #fff!important;
	}
	.MyTracks .item .text .infor{
		margin-bottom: 20rpx;
	}
	.topMeds{
		margin-top: 28rpx;
	}
	.noData{
		width: 690rpx;
		padding: 50rpx 30rpx;
		display: flex;
		height: 300rpx;
		justify-content: center;
		align-items: center;
		font-size: 26rpx;
		color: #292929;
		background: #fff;
	}
	.btquans{
		background: -webkit-linear-gradient(left, #ff630c 0%, #d30202 100%);
		background: linear-gradient(90deg, #ff630c 0%, #d30202 100%);
		border-radius: 40rpx;
		width: auto!important;
		font-size: 21rpx;
		color: #fff;
		margin-top: 3rpx;
		padding: 4rpx 20rpx 4rpx 50rpx;
		position: relative;
	}
	.inmg{
		width: 52rpx;
		height: 48rpx;
		position: absolute;
		left: 0;
		top: -6rpx;
	}
	.img72{
		width: 70rpx;
		height: 72rpx;
		margin-right: 20rpx;
	}
	.mask{
		width: 750rpx;
		height: 100vh;
		background: rgba(0,0,0,.5);
		position: fixed;
		left: 0;
		top: 0;
		z-index: 9999;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.boxView{
		width: 500rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		background: #fff;
		border-radius: 20rpx;
		padding: 40rpx 20rpx;
	}
	.boxView view{
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 32rpx;
	}
	.boxView view text{
		width: 40%;
		height: 60rpx;
		text-align: center;
		background: #999;
		border-radius: 40rpx;
		background: #999;
		line-height: 60rpx;
		color: #fff;
		margin-top: 60rpx;
		font-size: 28rpx;
		margin-right: 5%;
	}
	.boxView view text:last-child{
		background: #DB2121;
		margin-right: 0;
	}
	.clearDataHis{
		display: flex;
		justify-content: space-between;
		padding: 20rpx 30rpx;
		width: 690rpx;
		font-size: 24rpx;
		color: #292929;
		background: #f2f2f2;
	}
	.imgdel{
		width: 22rpx;
		height: 28rpx;
		margin-right: 10rpx;
	}
	.imgdispl{
		display: flex;
		align-items: center;
	}
	.order-item {
	    width: 100%;
	    display: flex;
	    position: relative;
		background: #fff;
	}
	.MyTracks .item .text .delete{
		font-size: 20rpx!important;
		border-radius:9px;
		border:1px solid #2b2b2b!important;
		height: auto!important;
		width: auto!important;
		line-height: normal!important;
		padding: 4rpx 20rpx;
		color: #2b2b2b!important;
	}
	.yj{
		margin-left: 10rpx;
	}
	.MyTracks .item .pictrue{
		width: 220rpx!important;
		height: 220rpx!important;
	}
	.MyTracks .item{
		border-bottom: none!important;
		width: 720rpx;
	}
	.MyTracks {
		border-top: none!important;
	}
	.MyTracks .item .text{
		height: 160rpx!important;
		padding: 70rpx 30rpx 50rpx 0;
		border-bottom: 1px solid #eee;
		width: 440rpx!important;
	}
	.remove {
	    width: 160rpx;
	    height: 100%;
	    background-color: #B5B5B5;
	    color: white;
	    position: absolute;
	    top: 0;
	    right: -160rpx;
	    display: flex;
	    justify-content: center;
	    align-items: center;
		font-size: 24rpx;
		letter-spacing: 1px;
	}
</style>
