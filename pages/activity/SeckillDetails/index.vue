<template>
  <view :class="[posterImageStatus ? 'noscroll product-con' : 'product-con']" v-show="domStatus">
    <product-con-swiper :imgUrls="imgUrls"></product-con-swiper>
	<image src="../../../static/imgs/mbg.png" mode="" class="mbg"></image>
    <view class="nav acea-row row-between-wrapper">
      <view>
		  <span class="btjins"><text class="fontBtj">可用补贴金</text><text class="colotex">{{storeInfo.otPrice-storeInfo.price}}</text></span>
		  <view class="money">
		    <text>￥</text>
		    <text class="num">{{storeInfo.price}}</text>
		  		<text class="daosho">到手价</text>
		    <text class="y-money">原价￥{{storeInfo.otPrice}}</text>
		  </view>
	  </view>
      
      <view class="acea-row row-middle">
		<view class="mbgaBox">
			<image src="../../../static/imgs/mbga.png" mode="" class="mbga" v-if="msTatus!=2"></image>
			<view class="timesd" v-if="msTatus!=2">
			  <view class="lqg">{{storeInfo.sales===storeInfo.stock?'已抢光':'立即抢购'}}</view>
			  <view class="jindus">
				  <view class="jindus1"></view>
				  <view class="jindus2" :style="{width: Math.round(storeInfo.sales/storeInfo.stock *100) + '%'}"></view>
				  <view class="jindus3">{{Math.round(storeInfo.sales/storeInfo.stock *100)}}%</view>
			  </view>
			  <view class="timerop">
			  	<view style="margin-right: 6rpx;">距结束</view>
			  	<count-down
			  	  :isDay="false"
			  	  :tipText="false"
			  	  :dayText="false"
			  	  :hourText="' : '"
			  	  :minuteText="' : '"
			  	  :secondText="false"
			  	  :datatime="datatime"
			  	></count-down>
			  </view>
			</view>
		</view>
      </view>
    </view>
	<view class="wrapper">
	  <view :class="{dian: !showCss}" class="introduce">
		{{ storeInfo.title }}
		<image v-if='shoppingName&& !btnshow' @click="showCon" src="../../../static/imgs/bottom.png" class="imgpic"></image>
		<image v-if='shoppingName&& btnshow' @click="showCon" src="../../../static/imgs/toop.png" class="imgpic"></image>
	  </view>
	  <view class="infopox">
		<font>{{ keyWord }}</font>
	    <text>月销:{{ storeInfo.sales }}{{ storeInfo.unitName }}</text>
	  </view>
		<view class="attribute acea-row row-between-wrapper" @click="tapBuy">
		  <view>
			<text>{{ attrTxt }}：</text>
			<text class="atterTxt">{{ attrValue }}</text>
		  </view>
		  <view class="linebox">...</view>
		</view>
		<view class="chengruo">
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  315认证
			</view>
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  全国联保
			</view>
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  7天无理由退换
			</view>
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  全场包邮
			</view>
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  7天退换
			</view>
			<view>
			  <image src="../../../static/imgs/item.png"></image>
			  48小时发货
			</view>
		</view>
	</view>
    <view class="product-intro">
      <view class="title">产品介绍</view>
      <view class="conter" v-html="storeInfo.description"></view>
    </view>
    <view style="height:100rpx;"></view>
    <view class="footerRush acea-row row-between-wrapper">
      <!-- <view
        class="customerSer acea-row row-center-wrapper row-column"
        @click="routerGo()"
      >
        <view class="iconfont icon-kefu"></view>
        <view>客服</view>
      </view> -->
      <!-- <view class="bnt bg-color-red" @click="tapBuy">立即购买</view> -->
	  <view class="footer acea-row row-between-wrapper goin">
	    <view class="item" @click="setCollect" v-if="userCollect">
	      <view class="iconfont icon-shoucang1"></view>
	      <text>收藏</text>
	    </view>
	    <view class="item" @click="setCollect" v-if="!userCollect">
	      <view class="iconfont icon-shoucang"></view>
	      <text>收藏</text>
	    </view>
	  		<view class="item">
	  			<button open-type="contact" @click="handleContact" class="buttConBig">
	  				<!-- <image src="../../../static/imgs/kefu.png" mode="" class="bimg"></image> -->
	  				<view class="iconfont icon-kefu"></view>
	  				<text>客服</text>
	  			</button>
	  		</view>
	    <view @click="goShoppingCart()" v-if="animated" class="item animated bounceIn">
	      <view class="iconfont icon-gouwuche1">
	        <text class="num bg-color-red" v-if="CartCount > 0">{{CartCount}}</text>
	      </view>
	      <text>购物车</text>
	    </view>
	    <view @click="goShoppingCart()" class="item animated" v-if="!animated">
	      <view class="iconfont icon-gouwuche1">
	        <text class="num bg-color-red" v-if="CartCount > 0">{{CartCount}}</text>
	      </view>
	      <text>购物车</text>
	    </view>
	    <view class="bnt acea-row">
	      <view class="joinCart" @click="joinCart" v-if="msTatus==1&&storeInfo.sales!=storeInfo.stock">
	        <text>加入购物车</text>
	      </view>
	      <view class="buy" @click="tapBuy" v-if="msTatus==1&&storeInfo.sales!=storeInfo.stock">
	        <text>立即购买</text>
	      </view>
		  <view class="daohuo" v-if="msTatus==1&&storeInfo.sales===storeInfo.stock" @click="daohuotongzhi(storeInfo)">
		    <text>到货通知</text>
		  </view>
		  <view class="jieshu" v-if="msTatus==0">
		    <text>已结束</text>
		  </view>
		  <view class="jijiangkaishi" v-if="msTatus==2">
		    <text>即将开始</text>
		  </view>
	    </view>
	  </view>
    </view>
    <ProductWindow v-on:changeFun="changeFun" :attr="attr" :cartNum="cartNum"></ProductWindow>
    <StorePoster
      v-on:setPosterImageStatus="setPosterImageStatus"
      :posterImageStatus="posterImageStatus"
      :posterData="posterData"
    ></StorePoster>
  </view>
</template>
<script>
import ProductConSwiper from "@/components/ProductConSwiper";
import CountDown from "@/components/CountDown";
import ProductWindow from "@/components/ProductWindow";
import StorePoster from "@/components/StorePoster";
import { getSeckillDetail } from "@/api/activity";
import { postCartAdd,getCartCount } from "@/api/store";
import { imageBase64 } from "@/api/public";
import { mapGetters } from "vuex";
import {
  getCoupon,
  getCollectAdd,
  getCollectDel,
  getUserInfo
} from "@/api/user";

const NAME = "SeckillDetails";

export default {
  name: "SeckillDetails",
  components: {
    ProductConSwiper,
    CountDown,
    ProductWindow,
    StorePoster
  },
  props: {},
  data: function() {
    return {
      domStatus: false,
      posterData: {
        image: "",
        title: "",
        price: "",
        code: ""
      },
	  isOpen: false,
	  btnshow: false,
      posterImageStatus: false,
	  userCollect: false,
	  animated: false,
	  showCss: false,
	  shoppingName: false,
	  msTatus: 0,
      action: "",
      imgUrls: [],
      storeInfo: [],
	  keyWord: '',
      replyCount: 0,
	  CartCount: 0,
      reply: [],
      cartNum: 1,
	  attrTxt: "请选择",
	  attrValue: "",
	  productValue: {},
      attr: {
        cartAttr: false,
        productSelect: {
          image: "",
          store_name: "",
          price: "",
          stock: "",
          unique: "",
          cart_num: 1
        },
		productAttr: [],
      },
      datatime: 0
    };
  },
  watch: {
    $yroute: function(n) {
      var that = this;
      if (n.name === NAME) {
        that.mountedStart();
		that.getCartCount(true);
      }
    }
  },
  computed: mapGetters(["isLogin", "location"]),
  mounted: function() {
    this.getCartCount(true);
    this.mountedStart();
	this.msTatus = this.$yroute.query.msTatus;
  },
  methods: {
	showCon(){
		let that=this
		that.showCss = !that.showCss
		that.btnshow = !that.btnshow
	},
	goShoppingCart() {
	  this.$yrouter.switchTab("/pages/shop/ShoppingCart/index");
	},
	daohuotongzhi(s){
		this.$yrouter.push({
		  path: '/pages/activity/DaoHuo/index',
		  query:{
			  proTitle: s.title,
			  id: s.id
		  }
		})
	},
    mountedStart: function() {
      var that = this;
      let id = that.$yroute.query.id;
      that.datatime = parseInt(that.$yroute.query.time);
      getSeckillDetail(id).then(res => {
         res.data.storeInfo.description = res.data.storeInfo.description.replace(
          /\<img/gi,
          '<img style="max-width:100%;height:auto;"'
        );
        that.$set(that, "storeInfo", res.data.storeInfo);
		that.$set(that, "userCollect", res.data.userCollect);
		that.$set(that, 'keyWord',res.data.keyWord)
        that.$set(that, "imgUrls", res.data.storeInfo.sliderImageArr);
        that.$set(that, "replyCount", res.data.replyCount);
        that.$set(that, "reply", res.data.reply);
		that.$set(that, "productValue", res.data.productValue);
		that.$set(that, "productAttr", res.data.productAttr);
		uni.setNavigationBarTitle({
		　　title:res.data.storeInfo.title
		})
		let productAttr = res.data.productAttr;
		let value = [];
		for (let i = 0; i < productAttr.length; i++) {
		  this.$set(productAttr[i], "index", 0);
		  value.push(productAttr[i].attrValueArr[0]);
		}
		let productSelect = res.data.productValue[value.sort().join(",")];
		//that.attr.productSelect = res.data.productValue[value.sort().join(",")];
		console.log(productSelect.unique)
		if(productSelect&& productAttr.length){
			this.$set(this, "attrValue", value.sort().join(","));
			this.$set(this, "attrTxt", "已选");
		}else{
			this.$set(this, "attrValue", "");
			this.$set(this, "attrTxt", "请选择");
		}
		// this.$set(this, "attrValue", value.sort().join(","));
		// this.$set(this, "attrTxt", "已选");
        that.posterData.image = that.storeInfo.image_base;
        that.updateTitle();
        if (that.storeInfo.title.length > 30) {
          that.posterData.title = that.storeInfo.title.substring(0, 30) + "...";
		  that.shoppingName = true
        } else {
          that.posterData.title = that.storeInfo.title;
		  that.shoppingName = false
        }
        that.posterData.price = that.storeInfo.price;
		that.posterData.otPrice = that.storeInfo.otPrice;
        that.posterData.code = that.storeInfo.code_base;
        that.setProductSelect(productAttr,productSelect.unique);
        that.domStatus = true;
      });
    },
    updateTitle() {
      // document.title = this.storeInfo.title || this.$yroute.meta.title;
    },
    setPosterImageStatus: function() {
      // var sTop = document.body || document.documentElement;
      // sTop.scrollTop = 0;
      this.posterImageStatus = !this.posterImageStatus;
    },
    //将父级向子集多次传送的函数合二为一；
    changeFun: function(opt) {
      if (typeof opt !== "object") opt = {};
      let action = opt.action || "";
      let value = opt.value === undefined ? "" : opt.value;
      this[action] && this[action](value);
    },
    changeattr: function(res) {
      var that = this;
      that.attr.cartAttr = res;
    },
    ChangeCartNum: function(res) {
      var that = this;
      if (res) {
        if (that.attr.productSelect.cart_num < that.storeInfo.stock) {
          that.attr.productSelect.cart_num++;
          this.cartNum++;
        }
      } else {
        if (that.attr.productSelect.cart_num > 1) {
          that.attr.productSelect.cart_num--;
          this.cartNum--;
        }
      }
    },
    setProductSelect: function(pt,unique) {
      var that = this;
      var attr = that.attr;
      attr.productSelect.image = that.storeInfo.image;
      attr.productSelect.store_name = that.storeInfo.title;
      attr.productSelect.price = that.storeInfo.price;
	  attr.productSelect.otPrice = that.storeInfo.otPrice;
      attr.productSelect.stock = that.storeInfo.stock;
	  attr.productSelect.unique = unique;
	  console.log(pt)
	  attr.productAttr = pt;
      attr.cartAttr = false;
      that.$set(that, "attr", attr);
    },
	//  点击加入购物车按钮
	joinCart: function() {
	  //0=加入购物车
	  // that.attr.cartAttr = !that.attr.cartAttr
	  this.goCat(0);
	},
	// 加入购物车；
	goCat: function(news) {
	  let that = this,
	    productSelect = that.productValue[that.attrValue];
	  //打开属性
	  if (that.attrValue) {
	    //默认选中了属性，但是没有打开过属性弹窗还是自动打开让用户查看默认选中的属性
	    that.attr.cartAttr = !that.isOpen ? true : false;
	  } else {
	    if (that.isOpen) that.attr.cartAttr = true;
	    else that.attr.cartAttr = !that.attr.cartAttr;
	  }
	  //只有关闭属性弹窗时进行加入购物车
	  if (that.attr.cartAttr === true && that.isOpen === false)
	    return (that.isOpen = true);
	  //如果有属性,没有选择,提示用户选择
	  if (
	    that.attr.productAttr.length &&
	    productSelect === undefined &&
	    that.isOpen === true
	  ) {
	    uni.showToast({
	      title: "产品库存不足，请选择其它",
	      icon: "none",
	      duration: 2000
	    });
	    return;
	  }
	  let q = {
		combinationId:0, 
		secKillId:that.storeInfo.id,
		bargainId:0,
	    productId: that.storeInfo.productId,
	    cartNum: that.attr.productSelect.cart_num,
	    new: news,
	    uniqueId:that.attr.productSelect !== undefined? that.attr.productSelect.unique: ""
	  };
	  postCartAdd(q)
	    .then(function(res) {
		  console.log(res)
	      that.isOpen = false;
	      that.attr.cartAttr = false;
	      if (news) {
	        that.$yrouter.push({
	          path: "/pages/order/OrderSubmission/index",
	          query: {
	            id: res.data.cartId
	          }
	        });
	      } else {
	        uni.showToast({
	          title: "添加购物车成功",
	          icon: "success",
	          duration: 2000,
	        });
			that.getCartCount(true);
	      }
	    })
	    .catch(error => {
	      that.isOpen = false;
	      uni.showToast({
	        title: error.msg,
	        icon: "none",
	        duration: 2000
	      });
	    });
	},
	//获取购物车数量
	getCartCount: function(isAnima) {
	  let that = this;
	  const isLogin = that.isLogin;
	  if (isLogin) {
	    getCartCount({
	      numType: 0
	    }).then(res => {
			console.log(res)
	      that.CartCount = res.data.count;
	      //加入购物车后重置属性
	      if (isAnima) {
	        that.animated = true;
	        setTimeout(function() {
	          that.animated = false;
	        }, 500);
	      }
	    });
	  }
	},
	//收藏商品
	setCollect: function() {
	  let that = this,
	    id = that.storeInfo.productId,
	    category = "product";
	  if (that.userCollect) {
	    getCollectDel(id, category).then(function() {
	      that.userCollect = !that.userCollect;
	    });
	  } else {
	    getCollectAdd(id, category).then(function() {
	      that.userCollect = !that.userCollect;
	    });
	  }
	},
    selecAttrTap: function() {
      this.cartAttr = true;
    },
    tapBuy: function() {
      var that = this;
      if (that.attr.cartAttr == false) {
        that.attr.cartAttr = !this.attr.cartAttr;
      } else {
        var data = {};
        data.productId = that.storeInfo.productId;
        data.cartNum = that.attr.productSelect.cart_num;
        data.uniqueId = that.attr.productSelect.unique;
        data.secKillId = that.storeInfo.id;
        data.new = 1;
        postCartAdd(data)
          .then(res => {
            that.$yrouter.push({
              path: "/pages/order/OrderSubmission/index",
              query: { id: res.data.cartId }
            });
          })
          .catch(err => {
            uni.showToast({
            	title: err.msg || err.response.data.msg|| err.response.data.message,
            	icon: 'none',
            	duration: 2000
            });
          });
      }
    }
  }
};
</script>
<style scoped lang="less">
.daohuo{
	width: 444rpx!important;
	text-align: center;
	line-height: 76rpx;
	border-radius: 50rpx;
	background: linear-gradient(90deg, #1571FC 0%, #11A0F0 100%);
}
.jijiangkaishi{
	width: 444rpx!important;
	text-align: center;
	line-height: 76rpx;
	border-radius: 50rpx;
	background: linear-gradient(90deg, #1571FC 0%, #11A0F0 90%);
	opacity: .5;
}
.jieshu{
	width: 444rpx!important;
	text-align: center;
	line-height: 76rpx;
	border-radius: 50rpx;
	background:linear-gradient(90deg,rgba(251,90,0,1) 0%,rgba(255,0,0,1) 100%)!important;
	opacity: .5;
}
.timesd{
	position: absolute;
	left: 0;
	top: 0;
}
.jindus{
	width: 174rpx;
	height: 22rpx;
	position: relative;
	margin: 0 auto;
}
.jindus1{
	width: 174rpx;
	height: 22rpx;
	position: absolute;
	left: 0;
	top: 0;
	background: linear-gradient(90deg, #FC5016 0%, #FF2E43 100%);
	border-radius: 5px;
	opacity: 0.3258;
	z-index: 10;
}
.jindus2{
	position: absolute;
	left: 0;
	top: 0;
	height: 22rpx;
	z-index: 20;
	background: linear-gradient(90deg, #FC5016 0%, #FF2E43 100%);
	border-radius: 5px;
}
.jindus3{
	position: absolute;
	left: 0;
	top: 0;
	height: 22rpx;
	z-index: 30;
	font-size: 16rpx;
	color: #fff;
	width: 174rpx;
	text-align: center;
}
.lqg{
	width: 240rpx;
	height: 40rpx;
	text-align: center;
	line-height: 40rpx;
	font-size: 26rpx;
	color: #DE1212;
	font-weight: 500;
	padding: 2rpx 0 2rpx 0;
}
.timerop{
	display: flex;
	align-items: center;
	color: #DE1212;
	font-size: 20rpx;
	width: 240rpx;
	justify-content: center;
	margin-top: 4rpx;
}
.mbgaBox{
	width: 240rpx;
	height: 134rpx;
	position: relative;
}
.fontBtj{
	color: #292929;
	font-size: 24rpx;
	opacity: 1;
}
.colotex{
	color: #DA0000;
	font-size: 30rpx;
	opacity: 1;
}
.btjins{
	height: 44rpx;
	background: #FCC499;
	border-radius: 12px;
	padding: 0 20rpx;
}
.product-con .nav .money .y-money{
	font-size: 24rpx!important;
}
.daosho{
	font-size: 20rpx;
	color: #fff;
	margin-left: 6rpx;
}
.noscroll {
  height: 100%;
  overflow: hidden;
}
.product-con .nav {
  padding: 0 0.2*100rpx;
}
.mbg{
	width: 750rpx;
	height: 134rpx;
	display: block;
}
.product-con .footerRush .bnt{
	width: auto!important;
	text-align: center;
	line-height: normal!important;
	height: auto!important;
}
.mbga{
	width: 240rpx;
	height: 138rpx;
	margin-top: -2rpx;
	display: block;
}
.product-con .nav{
	margin-top: -134rpx;
	height: 134rpx!important;
}
.product-con .footer .bnt .joinCart{
		background:linear-gradient(90deg,rgba(255,182,5,1) 0%,rgba(255,119,3,1) 100%)!important;
	}
	.product-con .footer .bnt .buy{
		background:linear-gradient(90deg,rgba(251,90,0,1) 0%,rgba(255,0,0,1) 100%)!important;
	}
	.bgGwc{
		background: #fff!important;
	}
	.goin{
		display: flex;
		align-items: center;
	}
	.buttConBig{
		background: none;
		display: block;
		width: 60rpx!important;
		font-size: 18rpx;
		line-height: 1.5;
		padding: 0;
		color: #666;
		border: none!important;
	}
	.buttConBig text{
		display: block;
		width: 60rpx;
	}
	.buttConBig::after {
	  border: none;
	  width: 60rpx!important;
	}
	.bimg{
		width: 32rpx;
		height: 32rpx;
		margin-top: 6rpx;
	}
	.product-con .wrapper{
		padding-bottom: 30rpx;
	}
	.linebox{
		font-size: 40rpx;
		color: #606060;
		margin-top: -20rpx;
		font-weight: bold!important;
	}
	.product-con .wrapper .introduce{
		margin: 0 30rpx!important;
		padding: 30rpx 50rpx 0 0;
		width: 640rpx;
		position: relative;
	}
	.dian{
		white-space: nowrap;
		text-overflow: ellipsis;
		overflow: hidden;
	}
	.imgpic{
		width: 28rpx;
		height: 16rpx;
		position: absolute;
		right: 0;
		top: 44rpx;
	}
	.infopox{
	  width: 690rpx;
	  display: flex;
	  align-items: center;
	  justify-content: space-between;
	  margin-top: 20rpx;
	  padding: 0 30rpx;
	}
	.infopox font{
	  color: #EEA801;
	  font-size: 20rpx;
	  padding: 2rpx 8rpx; 
	  position: relative;
	}
	.infopox font::after{
		position: absolute;
		content: '';
		border:1px solid #EEA801;
	    border-radius: 14rpx;
		width: 200%;
		height: 200%;
		top: 0;
		left: 0;
		transform: scale(0.5);
		transform-origin: 0 0;
	}
	.infopox text{
	  font-size: 26rpx;
	  color: #999;
	}
	.imgpoxCon{
		width: 80rpx;
		height: 56rpx;
		position: absolute;
		right: 0;
		top: 10rpx;
		z-index: 50;
		background: rgba(255,255,255,.5);
		border-radius: 40rpx 0 0 40rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.imgPicboc{
		width: 32rpx;
		height: 34rpx;
	}
	.boxc{
	  width: 710rpx;
	  height: 88rpx;
	  display: flex;
	  justify-content: space-between;
	  align-items: center;
	  position: absolute;
	  left: 0; 
	  top: 0;
	  padding: 10rpx 20rpx 0;
	}
	.boxc view{
	  display: flex;
	  color: #fff;
	}
	.line-line{
	  text-decoration: line-through;
	  margin-top: 16rpx;
	  font-size: 24rpx;
	}
	.boxc view font{
	  font-size: 22rpx;
	  margin-right: 10rpx;
	}
	.boxc-view-font{
	  font-size: 22rpx;
	  margin-right: 20rpx;
	  display: flex;
	  align-items: center;
	}
	.colotr{
		font-size: 22rpx!important;
		color: #292929;
	}
	.boxc view font text{
	  font-size: 40rpx;
	  margin-right: 6rpx;
	}
	.gyj{
	  width: 116rpx;
	  height: 98rpx;
	  margin-top: -28rpx;
	}
	.gwc{
	  width: 750rpx;
	  position: relative;
	  height: 108rpx;
	}
	.articles{
	  height:70rpx;
	  background:rgba(255,255,255,1);
	  border-radius:9px;
	  display: flex;
	  padding-right: 10rpx;
	  max-width: 400rpx;
	}
	.articles span{
	  color: #292929;
	  font-size: 26rpx;
	  display: flex;
	  align-items: center;
	}
	.articles font{
	  color: #DA0000;
	  font-size: 36rpx;
	}
	.gwcimg{
	  width: 750rpx;
	  height: 108rpx;
	}
	.product-con .store-info{
		margin-top: 0!important;
	}
	.product-con .attribute{
		margin-top: 40rpx!important;
		box-shadow:0px 0px 5px 0px rgba(39,0,0,0.06);
		width: 630rpx;
		margin-left: 30rpx;
		border-radius: 20rpx;
		margin-bottom: 20rpx;

	}
.chengruo{
  width: 630rpx;
  box-shadow:0px 0px 5px 0px rgba(39,0,0,0.06);
  display: flex;
  flex-wrap: wrap;
  padding: 40rpx 30rpx 20rpx 30rpx;
  border-radius: 20rpx;
  margin-top: 40rpx;
  margin-left: 30rpx;
}
.chengruo view{
  font-size: 24rpx;
  color: #292929;
  display: flex;
  align-items: center;
  width: 210rpx;
  margin-bottom: 20rpx;
  font-weight: 400;
}
.chengruo view image{
  width: 24rpx;
  height: 24rpx;
  margin-right: 10rpx;
}
.geoPage {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  z-index: 10000;
}

.product-con .store-info {
  margin-top: 0.2 * 100rpx;
  background-color: #fff;
}

.product-con .store-info .title {
  padding: 0 0.3 * 100rpx;
  font-size: 0.28 * 100rpx;
  color: #282828;
  height: 0.8 * 100rpx;
  line-height: 0.8 * 100rpx;
  border-bottom: 0.01 * 100rpx solid #f5f5f5;
}
.product-con .product-intro .title{
	
	background: #f5f5f5!important;
}
.product-con .store-info .info {
  padding: 0 0.3 * 100rpx;
  height: 1.26 * 100rpx;
}

.product-con .store-info .info .picTxt {
  width: 100%;
  display: flex;
  align-items: center;
}

.product-con .store-info .info .picTxt .pictrue {
  width: 0.76 * 100rpx;
  height: 0.76 * 100rpx;
  margin-right: 0.2 * 100rpx;
}

.product-con .store-info .info .picTxt .pictrue image {
  width: 100%;
  height: 100%;
  border-radius: 0.06 * 100rpx;
}

.product-con .store-info .info .picTxt .text {
  flex: 1;
}

.product-con .store-info .info .picTxt .text .name {
  font-size: 0.3 * 100rpx;
  color: #282828;
}

.product-con .store-info .info .picTxt .text .address {
  font-size: 0.24 * 100rpx;
  color: #666;
  margin-top: 0.03 * 100rpx;
}

.product-con .store-info .info .picTxt .text .address .iconfont {
  color: #707070;
  font-size: 0.18 * 100rpx;
  margin-left: 0.1 * 100rpx;
}

.product-con .store-info .info .picTxt .addressBox {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}
.product-con .store-info .info .picTxt .addressBox .iconfont {
  font-size: 0.4 * 100rpx;
}
.product-con .store-info .info .picTxt .addressBox .addressTxt {
  font-size: 0.24 * 100rpx;
  color: #eb3729;
}

.product-con .store-info .praise {
  font-size: 0.28 * 100rpx;
  color: #808080;
}
.product-con .store-info .praise .iconfont {
  font-size: 0.28 * 100rpx;
}

.product-con .superior {
  background-color: #fff;
  margin-top: 0.2 * 100rpx;
}

.product-con .superior .title {
  height: 0.98 * 100rpx;
}

.product-con .superior .title image {
  width: 0.3 * 100rpx;
  height: 0.3 * 100rpx;
}

.product-con .superior .title .titleTxt {
  margin: 0 0.2 * 100rpx;
  font-size: 0.3 * 100rpx;
  background-image: linear-gradient(to right, #f57a37 0%, #f21b07 100%);
  background-image: -webkit-linear-gradient(to right, #f57a37 0%, #f21b07 100%);
  background-image: -moz-linear-gradient(to right, #f57a37 0%, #f21b07 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.product-con .superior .slider-banner {
  width: 6.9 * 100rpx;
  margin: 0 auto;
  padding-bottom: 0.2 * 100rpx;
}

.product-con .superior .slider-banner .list {
  width: 100%;
  padding-bottom: 0.2 * 100rpx;
}

.product-con .superior .slider-banner .list .item {
  width: 2.15 * 100rpx;
  margin: 0 0.22 * 100rpx 0.3 * 100rpx 0;
  font-size: 0.26 * 100rpx;
}

.product-con .superior .slider-banner .list .item:nth-of-type(3n) {
  margin-right: 0;
}

.product-con .superior .slider-banner .list .item .pictrue {
  width: 100%;
  height: 2.15 * 100rpx;
}

.product-con .superior .slider-banner .list .item .pictrue image {
  width: 100%;
  height: 100%;
  border-radius: 0.06 * 100rpx;
}

.product-con .superior .slider-banner .list .item .name {
  color: #282828;
  margin-top: 0.12 * 100rpx;
}

.product-con .superior .slider-banner .swiper-pagination-bullet {
  background-color: #999;
}

.product-con .superior .slider-banner .swiper-pagination-bullet-active {
  background-color: #e93323;
}

.mask {
  -webkit-filter: blur(2px);
  -moz-filter: blur(2px);
  -ms-filter: blur(2px);
  filter: blur(2px);
}

.footer .icon-shoucang1 {
  color: #eb3729;
}

.product-con .product-intro .conter view {
  width: 100% !important;
}
.product-con .product-intro{
	margin-top: 0!important;
}
.generate-posters {
  width: 100%;
  height: 1.7 * 100rpx;
  background-color: #fff;
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 99;
  transform: translate3d(0, 100%, 0);
  -webkit-transform: translate3d(0, 100%, 0);
  -ms-transform: translate3d(0, 100%, 0);
  -moz-transform: translate3d(0, 100%, 0);
  -o-transform: translate3d(0, 100%, 0);
  transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
  -webkit-transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
  -moz-transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
  -o-transition: all 0.3s cubic-bezier(0.25, 0.5, 0.5, 0.9);
}

.generate-posters.on {
  transform: translate3d(0, 0, 0);
  -webkit-transform: translate3d(0, 0, 0);
  -ms-transform: translate3d(0, 0, 0);
  -moz-transform: translate3d(0, 0, 0);
  -o-transform: translate3d(0, 0, 0);
}

.generate-posters .item {
  flex: 50%;
  -webkit-flex: 50%;
  -ms-flex: 50%;
  text-align: center;
}

.generate-posters .item .iconfont {
  font-size: 0.8 * 100rpx;
  color: #5eae72;
}

.generate-posters .item .iconfont.icon-haibao {
  color: #5391f1;
}

.noscroll {
  height: 100%;
  overflow: hidden;
}
</style>
