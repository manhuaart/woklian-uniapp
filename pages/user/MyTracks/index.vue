<template>
  <view ref="container" class="pagesdwed">
	<view class="clearDataHis" v-if="storeInfo.length">
		<view class="imgdispl" @click="cancel">
			<image src="../../../static/imgs/dels.png" class="imgdel"></image>清空
		</view>
	</view>
	<scroll-view :scroll-y="isScroll" :style="{ height: windowHeight + 'px' }" v-if="storeInfo.length">
		<block :key="index" v-for="(items, index) in storeInfo">
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
	<view class="noData" v-if="!storeInfo.length">
		<image src="../../../static/imgs/123.png" class="img72" mode=""></image>
		暂无浏览记录，逛逛推荐吧～
	</view>
	<Recommend v-if="!storeInfo.length"></Recommend>
	<view class="mask" v-if='mkfalse'>
		<view class="boxView">
			<view>您要清空全部足迹吗？</view>
			<view>
				<text @click="cancel">取消</text>
				<text @click="clearStorage">确定</text>
			</view>
		</view>
	</view>
  </view>
</template>
<script>
import { getCollectUser, getCollectDel } from "@/api/user";
import Recommend from "@/components/Recommend";
import Loading from "@/components/Loading";
export default {
  name: "GoodsCollection",
  components: {
    Loading,
	Recommend,
  },
  props: {},
  data: function() {
    return {
	  storeInfo:[],
      page: 1,
      limit: 20,
	  // collectProductList:[],
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
	this.storeInfo = uni.getStorageSync('storeInfoHis');
	console.log(uni.getStorageSync('storeInfoHis'))
  },
  onReachBottom() {
    
  },
   onLoad: function(options) {
          var that = this;
          wx.getSystemInfo({
              success: function(res) {
                  that.windowHeight = res.windowHeight;
              }
          });
      },
  methods: {
	  cancel(){
	  	this.mkfalse = !this.mkfalse;
	  },
	  clearStorage(){
	  	this.mkfalse = !this.mkfalse;
	  	uni.setStorageSync('storeInfoHis', '');
		this.storeInfo = []
		uni.showToast({
		  title: "清空成功",
		  icon: "success",
		  duration: 2000,
		 })
	  },
	  drawStart: function(e) {
		  // console.log("drawStart");
		  var touch = e.touches[0];
		  for (var index in this.storeInfo) {
			  this.storeInfo[index].right = 0;
		  }
		  this.startX = touch.clientX;
	  },
	  drawMove: function(e) {
		  var touch = e.touches[0];
		  var item = this.storeInfo[e.currentTarget.dataset.index];
		  var disX = this.startX - touch.clientX;

		  if (disX >= 20) {
			  if (disX > this.delBtnWidth) {
				  disX = this.delBtnWidth;
			  }
			  this.isScroll = false;
			  this.storeInfo[e.currentTarget.dataset.index].right = disX;
		  } else {
			  this.isScroll = true;
			  this.storeInfo[e.currentTarget.dataset.index].right = 0;
		  }
	  },
	  drawEnd: function(e) {
		  var item = this.storeInfo[e.currentTarget.dataset.index];
		  if (item.right >= this.delBtnWidth / 2) {
			  this.isScroll = true;
			  this.storeInfo[e.currentTarget.dataset.index].right = this.delBtnWidth;
		  } else {
			  this.isScroll = true;
			  this.storeInfo[e.currentTarget.dataset.index].right = 0;
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
	//删除收藏；
	delCollection: function(index) {
		let that = this
	    
		uni.showToast({
		  title: "删除成功",
		  icon: "success",
		  duration: 2000,
		  complete: () => {
		    console.log(index)
			var newData = uni.getStorageSync('storeInfoHis');
			newData.splice(index,1)
			this.storeInfo = newData;
			uni.setStorageSync('storeInfoHis',newData);
			console.log(uni.getStorageSync('storeInfoHis'))
		  }
		});
	}
  }
};
</script>
<style scoped>
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
		justify-content: flex-end;
		padding: 10rpx 30rpx;
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
