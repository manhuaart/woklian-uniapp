<template>
	<view class="index">
		<view class="header  acea-row row-center-wrapper">
			<view class="boxcol">
				<view @click="goGoodSearch()" class="search acea-row row-middle">
					<text class="iconfont icon-xiazai5"></text>
					搜你想搜
				</view>
				<view class="qr" @click="startQr()">
					<image src="@/static/images/qr.png" />
				</view>
			</view>
		</view>
		<view class="fixed-header-box"></view>
		<image src="../../static/imgs/banbg.png" class="imgboxSel" mode=""></image>
		<view class="slider-banner banner">
			<swiper indicatorDots="true" indicator-active-color='#fff' indicator-color='rgba(255,255,255,0.5)' 
              current='0' interval='4000' duration='800' v-if="banner.length > 0" autoplay circular>
				<block v-for="(item, bannerIndex) in banner" :key="bannerIndex">
					<swiper-item>
						<view @click="goRoll(item)" class="swiper-item">
							<image :src="item.pic"/>
						</view>
					</swiper-item>
				</block>
			</swiper>
		</view>

		<view class="nav acea-row">
			<view @click="goWxappUrl(item)" class="item" v-for="(item, menusIndex) in menus" :key="menusIndex">
				<view class="pictrue">
					<image :src="item.pic" />
				</view>
				<view>{{ item.name }}</view>
			</view>
		</view>
		
		<!-- <view class="news acea-row ">
			<view class="pictrue" v-if="$VUE_APP_RESOURCES_URL">
				<image src="@/static/images/news.png" />
			</view>
			<view class="swiper-no-swiping new-banner">
				<swiper class="swiper-wrapper" v-if="roll.length > 0" :indicator-dots="false" autoplay circular vertical>
					<block v-for="(item, rollIndex) in roll" :key="rollIndex">
						<swiper-item class="swiper-slide">
							<view @click="goRoll(item)" class="swiper-item acea-row row-between-wrapper">
								<view class="text acea-row row-between-wrapper">
									<view class="label" v-if="item.show === '是'">最新</view>
									<view class="newsTitle line1">{{ item.info }}</view>
								</view>
								<view class="iconfont icon-xiangyou"></view>
							</view>
						</swiper-item>
					</block>
				</swiper>
			</view>
		</view> -->
		<!-- <view class="wrapper hot" v-if="likeInfo.length > 0"> -->
		<!-- <uni-notice-bar scrollable="true" @click="goRoll(singNew)" single="true" speed="10" showIcon="true" :text="singNew.info"></uni-notice-bar> -->
		<view class="wrapperBox">
		  <view class="wrapperCon">
			<view class="tuijian" :class="scrollTop > 30?'fiedc' : ''">
				<article @click='slect1' :class="{col: !bol1}">热门推荐<span v-if='!bol1'></span><i v-if='bol1'></i></article>
				<article @click='slect2' :class="{col: bol2}" >新品榜单<span v-if='bol2'></span><i v-if='!bol2'></i></article>
				<article @click='slect3' :class="{col: bol3}">品牌好货<span v-if='bol3'></span><i v-if='!bol3'></i></article>
			</view>
			<!-- 1 -->
			<view class="wrapper hot" v-if="likeInfo.length > 0 && !bol1">
			  <view class="title acea-row row-between-wrapper" v-if='scrollTop > 30'>
				<view class="text">
				  <view class="name line1">热门推荐</view>
				</view>
				<view
				  @click="goHotNewGoods(2)"
				  class="more"
				>
				  更多
				  <span class="iconfont icon-jiantou"></span>
				</view>
			  </view> 
			  <view class="listpox">
				<Hot-list :hot-list="likeInfo" :is-sort="false"></Hot-list>
			  </view>
			</view>
			<!-- 2 -->
			<view class="wrapper" v-if="firstList.length > 0 && bol2">
			  <view class="title acea-row row-between-wrapper" v-if='scrollTop > 30'>
				<view class="text">
				  <view class="name line1">
					新品榜单
					<span class="new font-color-red">NEW~</span>
				  </view>
				</view>
				<view
				  @click="goHotNewGoods(3)"
				  class="more"
				>
				  更多
				  <span class="iconfont icon-jiantou"></span>
				</view>
			  </view>
			  <view class="listpox">
				<New-list :new-list="firstList" :is-sort="false"></New-list>
			  </view>
			</view>
			<!-- 3 -->
			<view class="wrapper" v-if="bastList.length > 0 && bol3">
			  <view class="title acea-row row-between-wrapper" v-if='scrollTop > 30'>
				<view class="text">
				  <view class="name line1">品牌好货</view>
				</view>
				<view
				  @click="goHotNewGoods(1)"
				  class="more"
				>
				  更多
				  <span class="iconfont icon-jiantou"></span>
				</view>
			  </view>
			  <view class="listpox">
			  	<Good-list :good-list="bastList" :is-sort="false"></Good-list>
			  </view>
			</view>
			<!-- 3 -->
			
		  </view>
		</view>
		<!-- <view class="wrapper hot" v-if="bastList.length > 0">
			<image class="bg" src="../../static/images/index-bg.png" mode="widthFix"></image>
			<view class="title no-border acea-row row-between-wrapper">
				<view class="text line1">
					<span class="iconfont icon-remen"></span>
					<span class="label">热门榜单</span>
				</view>
				<view @click="goHotNewGoods(2)" class="more">
					更多
					<text class="iconfont icon-jiantou"></text>
				</view>
			</view>
			<view class="newProducts">
				<scroll-view :show-scrollbar="false" scroll-y="false" scroll-x="true">
					<view class="newProductsScroll">
						<view @click="goGoodsCon(item)" class="newProductsItem" v-for="(item, likeInfoIndex) in likeInfo" :key="likeInfoIndex">
							<view class="img-box">
								<image :src="item.image" />
							</view>
							<view class="pro-info line1"><text>{{ item.storeName }}</text></view>
							<view class="money font-color-red"><text>￥{{ item.price }}</text></view>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
		<view class="wrapper" v-if="bastList.length > 0">
			<view class="title no-border acea-row row-between-wrapper">
				<view class="text">
					<view class="name line1">
						 <span class="iconfont icon-jingpintuijian"></span>
			  <span class="label">精品推荐</span>
					</view>
				</view>
				<view @click="goHotNewGoods(1)" class="more">
					更多
					<text class="iconfont icon-jiantou"></text>
				</view>
			</view>
			<Good-list :good-list="bastList" :is-sort="false"></Good-list>
		</view>

		<view class="wrapper" v-if="firstList.length > 0">
			<view class="title acea-row row-between-wrapper">
				<view class="text">
					<view class="name line1">
						 <span class="iconfont icon-xinpin"></span>
			  			  <span class="label">首发新品</span>
					</view>
				</view>
				<view @click="goHotNewGoods(3)" class="more">
					更多
					<text class="iconfont icon-jiantou"></text>
				</view>
			</view>
			<view class="newProducts">
				<scroll-view :show-scrollbar="false" scroll-y="false" scroll-x="true">
					<view class="newProductsScroll">
						<view @click="goGoodsCon(item)" class="newProductsItem" v-for="(item, firstListIndex) in firstList" :key="firstListIndex">
							<view class="img-box">
								<image :src="item.image" />
							</view>
							<view class="pro-info line1">{{ item.storeName }}</view>
							<view class="money font-color-red">￥{{ item.price }}</view>
						</view>
					</view>
				</scroll-view>
			</view>
		</view> -->
		<!-- <view class="wrapper" v-if="benefit.length > 0">
			<view class="title acea-row row-center">
				<view class="text text-center">
					<view class="name line1 new-name">
              <span class="iconfont icon-shoucang"></span>
              <span class="txt">猜你喜欢</span>
            </view>
				</view>
				<!-- <view @click="goGoodsPromotion(4)" class="more">
					更多
					<text class="iconfont icon-jiantou"></text>
				</view> -->
			<!-- </view>
		</view> --> 
		<!-- <PromotionGood :benefit="benefit"></PromotionGood> -->
		<image src="../../static/imgs/top.png" class="imgtop" v-if='scrollTop > 30' @click='goTop' ></image>
		<Coupon-window :coupon-list="couponList" v-if="showCoupon" @checked="couponClose" @close="couponClose"></Coupon-window>
	</view>
</template>
<script>
	// import { swiper, swiperSlide } from "vue-awesome-swiper";
	import {
		mapState,
		mapMutations,
		mapActions
	} from 'vuex';
	
	import GoodList from '@/components/GoodList';
	import HotList from "@/components/HotList";
	import NewList from "@/components/NewList";
	import PromotionGood from '@/components/PromotionGood';
	import CouponWindow from '@/components/CouponWindow';
	import uniNoticeBar from '@/components/uni-notice-bar/uni-notice-bar.vue'
	import {
		getHomeData,
		getShare
	} from '@/api/public';
	import cookie from '@/utils/store/cookie';
	import {
		isWeixin,
		handleUrlParam
	} from '@/utils/index';

	const HAS_COUPON_WINDOW = 'has_coupon_window';
	

	export default {
		name: 'Index',
		components: {
			// swiper,
			// swiperSlide,
			uniNoticeBar,
			GoodList,
			HotList,
			NewList,
			PromotionGood,
			CouponWindow
		},
		props: {},
		data: function() {
			return {
				scrollTop: 0,
				getAdc: '',
			    bol1: false,
			    bol2: false,
			    bol3: false,
				showCoupon: false,
				logoUrl: '',
				banner: [],
				menus: [],
				roll: [],
				activity: [],
				activityOne: {},
				bastList: [],
				firstList: [],
				info: {
					fastList: [],
					bastBanner: [],

					bastList: []
				},
				likeInfo: [],
				lovely: [],
				benefit: [],
				couponList: [],
				swiperOption: {
					pagination: {
						el: '.swiper-pagination',
						clickable: true
					},
					autoplay: {
						disableOnInteraction: false,
						delay: 2000
					},
					loop: true,
					speed: 1000,
					observer: true,
					observeParents: true
				},
				swiperRoll: {
					direction: 'vertical',
					autoplay: {
						disableOnInteraction: false,
						delay: 2000
					},
					loop: true,
					speed: 1000,
					observer: true,
					observeParents: true
				},
				swiperScroll: {
					freeMode: true,
					freeModeMomentum: false,
					slidesPerView: 'auto',
					observer: true,
					observeParents: true
				},
				swiperBoutique: {
					pagination: {
						el: '.swiper-pagination',
						clickable: true
					},
					autoplay: {
						disableOnInteraction: false,
						delay: 2000
					},
					loop: true,
					speed: 1000,
					observer: true,
					observeParents: true
				},
				swiperProducts: {
					freeMode: true,
					freeModeMomentum: false,
					slidesPerView: 'auto',
					observer: true,
					observeParents: true
				}
			};
		},
		onPageScroll(e){
		      let _this = this;
		      this.scrollTop =e.scrollTop;
		},
		onShow: function() {
			this.getLocation()
			let that = this;
			// uni.showLoading({
			// 	title: '加载中'
			// });
			getHomeData().then(res => {
				that.logoUrl = res.data.logoUrl;
				that.$set(that, 'banner', res.data.banner);
				that.$set(that, 'menus', res.data.menus.reverse());
				that.$set(that, 'roll', res.data.roll);
				that.$set(that, 'info', res.data.info);
				that.$set(that, 'firstList', res.data.firstList);
				that.$set(that, 'bastList', res.data.bastList);
				that.$set(that, 'likeInfo', res.data.likeInfo);
				that.$set(that, 'lovely', res.data.lovely);
				that.$set(that, 'benefit', res.data.benefit);
				that.$set(that, 'couponList', res.data.couponList);
				// uni.hideLoading();
				that.setOpenShare();
			});
		},
		methods: {
			notice(){
				console.log(1)
				uni.requestSubscribeMessage({
					tmplIds: ["W8UR4WzPjRS-8aw5GIKgvW5rPU3_iki5C7J_W1gJYLk"],//数组
					success:(res)=>{
						console.log(2)
						console.log(res[tmplIds[0]]);//值包括'accept':同意、'reject':拒绝、'ban':后台禁用
					},
					fail:(err)=>{
					
					}
				})
			},
			goTop(){
			  if (wx.pageScrollTo) {
				wx.pageScrollTo({
				  scrollTop: 0
				})
			  } else {
				wx.showModal({
				  title: '提示',
				  content: '当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。'
				})
			  }
			},
			slect1(){
			  this.bol1 = false
			  this.bol2 = false
			  this.bol3 = false
			},
			slect2(){
			  this.bol1 = true
			  this.bol2 = true
			  this.bol3 = false
			},
			slect3(){
			  this.bol1 = true
			  this.bol2 = false
			  this.bol3 = true
			},
			...mapActions(["getLocation"]),
			
			goRoll(item) {
				if (item.url) {
					let urls =  encodeURIComponent(item.url)
					this.$yrouter.push({
						path: "/pages/user/share/index"
					})
					console.log(item.url)
					// uni.navigateTo({
					// 	url: '/pages/webView/index?url='+urls
					// })
				}
				if (item.uniapp_url) {
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: item.uniapp_url }
					});
				}
			},
			goGoodSearch() {
				// this.$yrouter.push('/pages/shop/GoodsEvaluate/index');
				this.$yrouter.push('/pages/shop/GoodSearch/index');
			},
			goWxappUrl(item) {
				if(item.name=='数码办公'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 11}
					});
				}else if(item.name=='户外运动'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 12}
					});
				}else if(item.name=='汽车用品'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 13}
					});
				}else if(item.name=='食品酒水'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 14}
					});
				}else if(item.name=='母婴用品'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 15}
					});
				}else if(item.name=='箱包配饰'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 16}
					});
				}else if(item.name=='美妆个护'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 17}
					});
				}else if(item.name=='家居百货'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 18}
					});
				}else if(item.name=='家用电器'){
					this.$yrouter.push({
					  path: "/pages/shop/GoodsList/index",
					  query: { id: 19}
					});
				}else if(item.name=='品牌故事'){
					this.$yrouter.push("/pages/user/BrandStory/index");
				}else{
					this.$yrouter.push(item.uniapp_url);
				}
				
			},
			goHotNewGoods(type) {
				this.$yrouter.push({
					path: '/pages/shop/HotNewGoods/index',
					query: {
						type
					}
				});
			},
			goGoodsCon(item) {
				this.$yrouter.push({
					path: '/pages/shop/GoodsCon/index',
					query: {
						id: item.id
					}
				});
			},
			goGoodsPromotion() {
				this.$yrouter.push('/pages/shop/GoodsPromotion/index');
			},
			setOpenShare: function() {},
			startQr: function() {
				uni.scanCode({
					success: (res) => {
						let option = handleUrlParam(res.result)
						console.log(option)


						// {productId: "19", spread: "21", codeType: "routine"}
						// {productId: "19", spread: "21", pageType: "good", codeType: "routine"}
						switch (option.pageType) {
							case 'good':
								// 跳转商品详情
								this.$yrouter.push({
									path: '/pages/shop/GoodsCon/index',
									query: {
										q: res.result
									}
								});
								break;
							case 'group':
								// 跳转团购
								this.$yrouter.push({
									path: '/pages/activity/GroupRule/index',
									query: {
										q: res.result
									}
								});
								break;
							case 'dargain':
								// 跳转砍价
								this.$yrouter.push({
									path: '/pages/activity/DargainDetails/index',
									query: {
										q: res.result
									}
								});
								break;
							default:
								// 跳转分销
								this.$yrouter.push({
									path: '/pages/index/index',
									query: {

									}
								});
								break;
						}


					}
				});
			}
		}
	};
</script>
<style scoped lang="less">
	.listpox{
		width: 642rpx;
		padding: 0 24rpx;
	}
	.imgboxSel{
		width: 750rpx;
		height: 100rpx;
	}
	.imgtop{
	  width: 52rpx;
	  height: 52rpx;
	  position: fixed;
	  bottom: 20rpx;
	  right: 30rpx;
	  z-index: 9999;
	}
	.index .nav .item{
	  width: 120rpx!important;
	  text-align: center;
	  font-size: 24rpx;
	  height: 128rpx;
	  margin-top: 30rpx;

	}
	.index .nav .item:first-child{
		margin-left: 0;
	}
	.index .nav .item:nth-child(6){
		margin-left: 0;
	}
	.index .wrapper .title{
		border-top: none!important;
	}
	.index .nav{
	  display: flex;
	  justify-content: space-between;
	  flex-wrap: wrap;
	  background: #f2f2f2;
	  width: 690rpx;
	  padding: 0 30rpx 30rpx 30rpx;
	  margin-bottom: 0!important
	}
	.index .nav .item .pictrue{
	  width: 94rpx!important;
	  height: 94rpx!important;
	}
	.index .hot .title .text .name {
	  color: #333!important;
	}
	.index .hot .title .more {
	  color: #333!important;
	}
	.index .hot .title .more .iconfont {
	  color: #333!important;
	}
	.index .wrapper .title{
	  padding-bottom: 34rpx;
	}
	.tuijian{
	  width: 95%;
	  display: flex;
	  padding-left: 5%;
	  align-items: center;
	}
	.tuijian article{
	  color:#292929;
	  font-size: 14px;
	  padding: 10px 0;
	  margin-right:20px;
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	}
	.tuijian article span{
	  width: 20px;
	  height: 3px;
	  background: #DE1B05;
	  border-radius: 20px;
	  margin-top: 2px;
	  box-shadow: 0 2px 6px #ddd;
	}
	.tuijian article i{
	  width: 20px;
	  height: 3px;
	  background: #fff;
	  border-radius: 20px;
	  margin-top: 2px;
	}
	.col{
	  color:#DE1B05!important;
	  font-size:16px!important;
	  font-weight: 700;
	}
	.fiedc{
	  position: fixed;
	  left: 0;
	  top: 0;
	  z-index: 99999;
	  background: #fff;
	  box-shadow: 0 2px 6px #e2e2e2;
	}
	.fiedc .col{
	  font-size: 14px!important;
	}
	.wrapperBox{
	  background: #f2f2f2;
	  padding: 0 30rpx;
	  width: 690rpx;
	}
	.wrapperCon{
	  background: #fff!important;
	  border-radius: 16px;
	}
	.wrapper{
	  width: 690rpx!important;
	}
	.index {
		background-color: #f2f2f2;
	}
	.boxcol{
		width: 690rpx;
		height: 64rpx;
		border-radius: 50rpx;
		background: #fff;
		display: flex;
		align-items: center;
	}
	.swiper-item {
		height: 100%;
		border-radius: 18rpx;
	}
	.index .header .search{
		background: none!important;
		width: 600rpx;
		flex: none!important;
	}
	.fixed-header {
		position: fixed;
		z-index: 99;
		top: 0;
		left: 0;
		right: 0;
		background: #fff;
		box-shadow: 0 0 20rpx -10rpx #aaa;

		&+.fixed-header-box {
			height: 98rpx
		}
	}
	.index .header{
		background: #D30202!important;
	}	
	.index .banner{
	  width: 690rpx!important;
	  padding: 0 30rpx!important;
	  height: 278rpx;
	  // background: url(../../assets/images/banbg.png) no-repeat center top #f2f2f2!important;
	  background-size: 100%!important;
	  margin-top: -100rpx;
	}
	.index .banner swiper{
		height: 278rpx;
		border-radius: 18rpx;
	}
	.index .banner image {
		width: 690rpx;
		height: 278rpx!important;
		border-radius: 18rpx;
	}
</style>
