<template>
  <view :class="[posterImageStatus ? 'noscroll product-con' : 'product-con']" v-show="domStatus">
    <product-con-swiper :imgUrls="imgUrls"></product-con-swiper>
	  <view class="gwc">
	    <image src="../../../static/imgs/gwc.png" class="gwcimg" ></image>
	    <view class="boxc">
			<view>
			  <font>￥<text>{{ storeInfo.price }}</text>到手价</font>
			  <text class="line-line">原价￥{{ storeInfo.productPrice }}</text>
			</view>
			<view class="articles">
			  <image src="../../../static/imgs/gyj.png" class="gyj" ></image>
			  <font class='boxc-view-font'><text class='colotr'>可用补贴券</text> <text style="margin-right: 0; font-size: 36rpx;">{{storeInfo.productPrice - storeInfo.price}}</text></font>
			</view>
	    </view>
	  </view>
    <view class="wrapper">
<!--      <view class="share acea-row row-between row-bottom">
        <view class="money font-color-red">
          ￥
          <text class="num" v-text="storeInfo.price"></text>
          <text class="y-money" v-text="'￥' + storeInfo.productPrice"></text>
        </view>
      </view> -->
      <view :class="{dian: !showCss}" class="introduce">
		{{ storeInfo.title }}
		<image v-if='shoppingName&& !btnshow' @click="showCon" src="../../../static/imgs/bottom.png" class="imgpic"></image>
		<image v-if='shoppingName&& btnshow' @click="showCon" src="../../../static/imgs/toop.png" class="imgpic"></image>
	  </view>
      <!-- <view class="introduce" v-text="storeInfo.title"></view> -->
	  <!-- <view class="infopox">
        <text v-text="'类型:' + storeInfo.people + '人团'"></text>
        <text v-text="'库存:' + storeInfo.stock + storeInfo.unitName"></text>		  
		<text v-text="'已拼' + storeInfo.sales + storeInfo.unitName"></text>
	  </view> --> 
	  <view class="infopox">
	  		<font>{{ keyWord }}</font>
	    <text>已拼:{{ storeInfo.sales }}{{ storeInfo.unitName }}</text>
	  </view>
		<view class="attribute acea-row row-between-wrapper" @click="tapBuy">
		  <view>
			<text>{{ attrTxt }}：</text>
			<text class="atterTxt">{{ attrValue }}</text>
		  </view>
		  <view class="linebox">...</view>
		</view>
<!-- 	  <view class="attribute acea-row row-between-wrapper" @click="tapBuy">
	    <view>
	  	<text>{{ attrTxt }}：</text>
	  	<text class="atterTxt">{{ attrValue }}</text>
	    </view>
	    <view class="linebox">...</view>
	  </view> -->
    </view>
<!--    <view class="notice acea-row row-middle">
      <view class="num font-color-red">
        <text class="iconfont icon-laba"></text>
        已拼{{ storeInfo.sales
        }}{{ storeInfo.unitName }}
        <text class="line">|</text>
      </view>
      <view class="swiper-no-swiping swiper">
        <swiper class="swiper-wrapper" :options="swiperTip" :autoplay="true" :interval="3000">
          <block v-for="(item, itemNewIndex) in itemNew" :key="itemNewIndex">
            <swiper-item>
              <view class="line1">{{ item }}</view>
            </swiper-item>
          </block>
        </swiper>
      </view>
    </view> -->
	<!-- 发起拼团 -->
    <view class="assemble">
	   <view class="heard acea-row row-between-wrapper" v-if="groupList.length<1">
			<view class="txtone">暂无拼友哦~可自主发起拼单!</view>
	   </view>
      <view class="heard acea-row row-between-wrapper" v-else>
		  <view class="txtone">{{groupList.length}}人正在拼团，可直接参与</view>
		  <view class="txttwo  acea-row row-between-wrapper"  @click="developAll" >
			  <text>展开全部</text>
			  <text class="iconfont icon-jiantou" v-show="show"></text>
			  <text class="iconfont icon-xiangxia"v-show="hide"></text>
		  </view>  
      </view>
	 <view  v-show="show">  
      <view v-for="(item, groupListindex) in groupList" :key="groupListindex">
        <view class="item acea-row row-between-wrapper" v-if="groupListindex < groupListCount">
          <view class="pictxt acea-row row-between-wrapper">
            <view class="pictrue">
              <image :src="item.avatar" class="image" />
            </view>
            <view class="text line1" v-text="item.nickname"></view>
          </view>
          <view class="right acea-row row-middle">
              <view class="lack">
                <text>还差</text>
                <text class="font-color-red" v-text="item.count"></text>
				<text class="font-color-red">人</text>
                <text>拼成</text>
              </view>
		      <count-down
				:isDay="false"
				:tipText="'剩余 '"
				:dayText="false"
				:hourText="':'"
				:minuteText="':'"
				:secondText="false"
				:datatime="item.stopTime/1000"
			  ></count-down>
			  <view class="spellBnt" @click="groupRule(item.id,item.avatar,item.stopTime,item.nickname,item.count)"> 去拼单 </view>
          </view>
        </view>
      </view>
	  </view>

	 <view :class="{'swiper-pintuan': groupList.length>0 && groupList.length>4}" v-show="hide">  
      <view v-for="(item, groupListindex) in groupList" :key="groupListindex">
		 <view class="item acea-row row-between-wrapper"> 
        <!-- <view class="item acea-row row-between-wrapper" v-if="groupListindex < groupListCount"> -->
          <view class="pictxt acea-row row-between-wrapper">
            <view class="pictrue">
              <image :src="item.avatar" class="image" />
            </view>
            <view class="text line1" v-text="item.nickname"></view>
          </view>
          <view class="right acea-row row-middle">
              <view class="lack">
                <text>还差</text>
                <text class="font-color-red" v-text="item.count"></text>
				<text class="font-color-red">人</text>
                <text>拼成</text>
              </view>
		      <count-down
				:isDay="false"
				:tipText="'剩余 '"
				:dayText="false"
				:hourText="':'"
				:minuteText="':'"
				:secondText="false"
				:datatime="item.stopTime/1000"
			  ></count-down>
			  <view class="spellBnt" @click="groupRule(item.id,item.avatar,item.stopTime,item.nickname,item.count)"> 去拼单 </view>
          </view>
        </view>
      </view>
	  </view>
     <!-- <view class="more" v-if="groupList.length > groupListCount" @click="setGroupListCount">
        查看更多
        <text class="iconfont icon-xiangxia"></text>
      </view> -->
    </view>
    <view class="userEvaluation">
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
      <view class="title acea-row row-between-wrapper">
        <view v-text="'用户评价(' + replyCount + ')'"></view>
        <view class="praise" @click="goReply">
          <text class="font-color-red" v-text="replyChance + '%'"></text>好评率
          <text class="iconfont icon-jiantou"></text>
        </view>
      </view>
      <UserEvaluation :reply="reply"></UserEvaluation>
    </view>
    <view class="product-intro">
      <view class="title">产品介绍</view>
      <view class="conter" v-html="storeInfo.description"></view>
      <!-- <view class="conter" v-html=""></view> -->
    </view>
    <view style="height:100rpx;"></view>
    <view class="footer-group acea-row row-between-wrapper">
      <view class="customerSer acea-row row-center-wrapper row-column" @click="setCollect">
        <view class="iconfont" :class="userCollect ? 'icon-shoucang1' : 'icon-shoucang'"></view>
        <text>收藏</text>
      </view>
     <view class="customerSer acea-row row-center-wrapper row-column">
	  		<button open-type="contact" @click="handleContact" class="buttConBig">
	  			<view class="iconfont icon-kefu"></view>
	  			<text>客服</text>
	  		</button>		
      </view>
<!-- ----------------------------- -->
	    <view  class="customerSer acea-row row-center-wrapper row-column" style="position: relative;"  @click="goShoppingCart()" >
		    <view class="iconfont icon-gouwuche1">
		      <text class="num bg-color-red" v-if="CartCount > 0">{{CartCount}}</text>
		    </view>
	      <text>购物车</text>
	    </view>
<!-- ----------------------------- -->
        <view class="bnt acea-row">
          <view class="joinCart" @click="openAlone">
            <text>￥</text>
			<text class="textt">{{storeInfo.sellPrice}}</text>
			<text class="texth">单独购买</text>
          </view>
          <view class="buy" @click="openTeam">
			<text>￥</text>
			<text class="textt">{{storeInfo.price}}</text>
			<text class="texth">发起拼单</text>
          </view>
        </view>
    </view>
    <ProductWindow v-if="cartNum" v-on:changeFun="changeFun" :attr="attr" :cartNum="cartNum"></ProductWindow>
    <StorePoster
      v-on:setPosterImageStatus="setPosterImageStatus"
      :posterImageStatus="posterImageStatus"
      :posterData="posterData"
    ></StorePoster>
	<view class="maskPt" v-if='ptuan'>
		<view class="maskNr">
			  <view class="title acea-row row-center-wrapper">
				<view class="name">
				  <text>参与{{maskNickname}}的拼单</text>
				</view>
			  </view>
			  <view class="tips-warp">
				<view class="tips  acea-row row-center-wrapper">
					 <text>仅剩{{maskCount}}个名额，</text>		 
					  <count-down
						:isDay="false"
						:tipText="false"
						:dayText="false"
						:hourText="' : '"
						:minuteText="' : '"
						:secondText="false"
						:datatime="maskStopTime/1000"
					  ></count-down>
					  <text>后结束</text>
				 </view>
			  </view>	
			  <view  class="list acea-row row-middle">
			    <view class="pictrue">
			  			<view class="rank">拼主</view>
			        <image :src="maskAvatar" />
			    </view>
				<view class="pictrue">
					<image class="img-none" src="@/static/images/vacancy.png" />
				</view>
			  </view>
			  <view class="teamBnt" @click="groupMask(maskId)">参与拼单</view>
			  <view class="close" @click="closeMaskPt">
			  	<image src="@/static/images/close.png" />
			  </view>
		</view>
	</view>
  </view>
</template>

<script>
// import { swiper, swiperSlide } from "vue-awesome-swiper";
import ProductConSwiper from "@/components/ProductConSwiper";
import CountDown from "@/components/CountDown";
import UserEvaluation from "@/components/UserEvaluation";
import ProductWindow from "@/components/ProductWindow";
import StorePoster from "@/components/StorePoster";
import { getCombinationDetail } from "@/api/activity";
import { postCartAdd,getCartCount } from "@/api/store";
import { imageBase64 } from "@/api/public";
import { mapGetters } from "vuex";
import {
  getCoupon,
  getCollectAdd,
  getCollectDel,
  getUserInfo
} from "@/api/user";
const NAME = "GroupDetails";

export default {
  name: "GroupDetails",
  components: {
    ProductConSwiper,
    CountDown,
    UserEvaluation,
    // swiper,
    // swiperSlide,
    ProductWindow,
    StorePoster
  },
  props: {},
  data: function() {
    return {
		CartCount: 0,
		attrTxt: "请选择",
		attrValue: "",
		shoppingName: false,
		showCss: false,
	  maskCount:undefined,
	  maskNickname:undefined,
	  maskStopTime:undefined,
	  maskAvatar:undefined,
	  maskId:undefined,
	  ptuan: false,
	  show:true,
	  hide:false,
	  btnshow: false,
      domStatus: false,
	  keyWord: '',
      posterData: {
        image: "",
        title: "",
        price: "",
        code: ""
      },
      posterImageStatus: false,
      reply: [],
      replyCount: 0,
      replyChance: 0,
      imgUrls: [],
      storeInfo: {},
      itemNew: {},
      groupListCount: 2,
      groupList: {},
      swiperTip: {
        direction: "vertical",
        autoplay: {
          disableOnInteraction: false,
          delay: 2000
        },
        loop: true,
        speed: 1000,
        observer: true,
        observeParents: true
      },
      attr: {
        cartAttr: false,
        productSelect: {
          image: "",
          store_name: "",
          price: "",
          stock: "",
          unique: "",
          cart_num: 1,
		  otPrice:"",
        }
      },
      cartNum: 1,
      userCollect: false
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
  },
  methods: {
	goShoppingCart() {
	  this.$yrouter.switchTab("/pages/shop/ShoppingCart/index");
	},
	  showCon(){
	  	let that=this
	  	that.showCss = !that.showCss
		that.btnshow = !that.btnshow
	  },
		closeMaskPt:function(){
			 this.ptuan =!this.ptuan;
		},
	  //展开全部
	  developAll:function(){
		    this.show =!this.show;
			this.hide =!this.hide;
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
	  },
    openAlone: function() {
      this.$yrouter.push({
        path: "/pages/shop/GoodsCon/index",
        query: { id: this.storeInfo.productId }
      });
      // this.$yrouter.replace({ path: "/detail/" + this.storeInfo.productId });
    },
// ---------------------------------------
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
    mountedStart: function() {
      var that = this;
      let id = that.$yroute.query.id;
      getCombinationDetail(id).then(res => {
        that.userCollect = res.data.userCollect;
        res.data.storeInfo.description = res.data.storeInfo.description.replace(
          /\<img/gi,
          '<img style="max-width:100%;height:auto;"'
        );
        that.$set(that, "storeInfo", res.data.storeInfo);
        that.$set(that, "imgUrls", res.data.storeInfo.sliderImageArr);
        that.$set(that, "itemNew", res.data.pinkOkList);
		that.$set(that, 'keyWord',res.data.keyWord);
        that.$set(that, "groupList", res.data.pink);
        that.$set(that, "reply", [res.data.reply]);
        that.$set(that, "replyCount", res.data.replyCount);
        that.$set(that, "replyChance", res.data.replyChance);
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
			console.log(value.sort().join(","))
			this.$set(this, "attrValue", value.sort().join(","));
			this.$set(this, "attrTxt", "已选");
		}else{
			this.$set(this, "attrValue", "");
			this.$set(this, "attrTxt", "请选择");
		}
        that.posterData.image = that.storeInfo.image;
        if (that.storeInfo.title.length > 30) {
          that.posterData.title = that.storeInfo.title.substring(0, 30) + "...";
		  that.shoppingName = true
        } else {
          that.posterData.title = that.storeInfo.title;
		  that.shoppingName = false
        }
        that.posterData.price = that.storeInfo.price;
        that.posterData.code = that.storeInfo.code_base;
        that.domStatus = true;
		that.setProductSelect(productAttr,productSelect.unique);
        //that.getImageBase64();
      });
    },
    getImageBase64: function() {
      let that = this;
      imageBase64(this.posterData.image, that.posterData.code).then(res => {
        that.posterData.image = res.data.image;
        that.posterData.code = res.data.code;
      });
    },
    setPosterImageStatus: function() {
      // var sTop = document.body || document.documentElement;
      // sTop.scrollTop = 0;
      this.posterImageStatus = !this.posterImageStatus;
    },
	//去拼单
    groupRule: function(id,avatar,stopTime,nickname,count) {
      var that = this;
	  // that.maskId=id;
	  // that.maskAvatar=avatar;
	  // that.maskStopTime=stopTime;
	  // that.maskNickname=nickname;
	  // that.maskCount=count;
      that.$yrouter.push({
        path: "/pages/activity/GroupRule/index",
        query: { id }
      });
	  // that.ptuan = !that.ptuan
    },
	// 弹框中去拼单
    groupMask: function(id) {
		console.log(id)
        var that = this;
        that.$yrouter.push({
        path: "/pages/activity/GroupRule/index",
        query: { id }
      });
	  that.ptuan = !that.ptuan
    },	
	
    goReply: function() {
      var that = this;
      that.$yrouter.push({
        path: "/pages/shop/EvaluateList/index",
        query: { id: that.storeInfo.productId }
      });
    },
    setGroupListCount: function() {
      this.groupListCount = this.groupListCount + 2;
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
      that.attr.productSelect.cart_num = 1;
      that.cartNum = 1;
      uni.showToast({
        title: "每人每次限购1" + that.storeInfo.unitName,
        icon: "none",
        duration: 2000
      });
    },
    setProductSelect: function(pt,unique) {
      var that = this;
      var attr = that.attr;
      attr.productSelect.image = that.storeInfo.image;
      attr.productSelect.store_name = that.storeInfo.title;
      attr.productSelect.price = that.storeInfo.price;
      attr.productSelect.stock = that.storeInfo.stock;
	  attr.productSelect.otPrice = that.storeInfo.productPrice;
	  attr.productSelect.unique = unique;
	  console.log(pt)
	  attr.productAttr = pt;
      attr.cartAttr = false;
      that.$set(that, "attr", attr);
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
    openTeam: function() {
      var that = this;
      if (that.attr.cartAttr == false) {
        that.attr.cartAttr = !this.attr.cartAttr;
      } else {
        var data = {};
        data.productId = that.storeInfo.productId;
        data.cartNum = that.attr.productSelect.cart_num;
        data.uniqueId = that.attr.productSelect.unique;
        data.combinationId = that.storeInfo.id;
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
              title:
                err.msg || err.response.data.msg || err.response.data.message,
              icon: "none",
              duration: 2000
            });
          });
      }
    }
  }
};
</script>
<style scoped lang="less">

	.buttConBig{
		background: none;
		display: block;
		width: 60rpx!important;
		font-size: 20rpx;
		line-height: 1.1;
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
.footer-group .icon-shoucang1 {
  color: #eb3729;
}
	.footer-group .bnt .joinCart{
		font-size:24rpx!important;
		background:linear-gradient(90deg,rgba(255,182,5,1) 0%,rgba(255,119,3,1) 100%)!important;
		.textt{font-size:34rpx!important;margin-left: -1rpx!important;}
		.texth{font-size:24rpx!important;margin-left: 1.1rpx!important;}
	}
	.footer-group .bnt .buy{
		font-size:24rpx!important;
		.textt{font-size:34rpx!important;margin-left: -1rpx!important;}
		.texth{font-size:24rpx!important;margin-left: 1.2rpx!important;}
		background:linear-gradient(90deg,rgba(251,90,0,1) 0%,rgba(255,0,0,1) 100%)!important;
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
	.wrapper .introduce{
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
		width: 28rpx;height: 16rpx;
		position: absolute;
		right: 0; top: 44rpx;
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
	
.maskPt{
	width: 100%;
	height: 100vh;
	position:fixed;
	left: 0;top: 0;
	z-index: 100;
	background: rgba(0,0,0,.5);
	display: flex;
	justify-content: space-around;
	.maskNr{
		width: 588rpx;
		height: 388rpx; 
		border-radius: 22rpx;
		color: #333333;
		background-color: #fff;
		margin-top: 210rpx;
		position: relative;
        .title{ 
			 margin-top: 38rpx;
	         .name {
		         font-size: 0.35*100rpx;
		         color:#333333;
		     }	
		}
		 .tips-warp .tips{
			    margin-top: 5rpx;
				font-size: 0.22*100rpx;
				text-align: center;
				color: #666666;
				letter-spacing: 0.01*100rpx;
				.time {
				 	font-size: 22rpx;
				 	margin-left: 10rpx;
				}
		}		
		.list {
			display: flex;
			justify-content:center;
			padding: 0 0.3*100rpx;
			margin-top: 0.36*100rpx;
			  .pictrue {
			  width: 0.84*100rpx;
			  height: 0.84*100rpx;
			  // margin: 0 0 0.29*100rpx 0.35*100rpx;
			  margin-left: -0.28*100rpx;
			  border-radius: 50%;
			}	
			.pictrue:nth-of-type(1){
				 position: relative;
				  margin-left: 0;
				 .rank{
					 position: absolute;
					 left:0;right:0;
					 margin:0 auto;
					 top:-0.05*100rpx;
					 width: 64rpx;
					 height: 28rpx;
					 line-height:29rpx;
					 background: #FFFFFF;
					 border-radius: 14rpx;
					 border: 2.6rpx solid #FFA600;
					 font-size: 20rpx;
					 color: #000000;
					 text-align: center;
					 z-index: 555;
				 }
			}	
			.pictrue image {
			        width: 100%;
			        height: 100%;
		        	border-radius: 50%;
		 	}
		    .pictrue image.img-none {  border: none;}			
							
		}
		.teamBnt {
			  position: absolute;
			  bottom:36rpx;
			  left: 0;right: 0;
			  font-size: 0.32*100rpx;
			  width: 400rpx;
			  height: 0.64*100rpx;
			  border-radius: 0.5*100rpx;
			  background: #DB2121;
			  text-align: center;
			  line-height: 0.64*100rpx;
			  color: #fff;
			  margin: 0 auto;
		}
		.close{
			position: absolute;
			bottom:-112rpx;
			left: 0;right: 0;
			margin: 0 auto;
			width: 76rpx;
			height: 76rpx;
			image{
				width: 100%;
				height: 100%;
			}
		}
	
	}
}


.product-con .wrapper .attribute{
		margin-top: 40rpx!important;
		box-shadow:0px 0px 5px 0px rgba(39,0,0,0.06);
		width: 630rpx;
		margin-left: 30rpx;
		border-radius: 20rpx;
		margin-bottom: 20rpx;
		.atterTxt {
		  font-size: 0.28*100rpx;
		  color: #282828;
		}
	    .iconfont {
	     font-size: 0.28*100rpx;
	     color: #7a7a7a;
	   }
	  .linebox{
			font-size: 40rpx;
			color: #606060;
			margin-top: -20rpx;
			font-weight: bold!important;
	  }	   
	}
.product-con .wrapper {
  padding-bottom: 0.26 * 100rpx;
}
.noscroll {
  height: 100%;
  overflow: hidden;
}
// .product-con .footer-group .bnt {
//   // flex:1;
//   // width: 43%;
// }
.product-con .footer-group .bnt.bg-color-violet {
  background-color: #fa8013;
}

// ---------
.product-con .userEvaluation {
  padding-top: 0.3*100rpx;
}
.chengruo{
  width: 630rpx;
  box-shadow:0px 0px 5px 0px rgba(39,0,0,0.06);
  display: flex;
  flex-wrap: wrap;
  padding: 40rpx 30rpx 20rpx 30rpx;
  border-radius: 20rpx;
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
.num {
	color: #fff;
	position: absolute;
	font-size: 18rpx;
	border-radius: 200rpx;
	top: -10rpx;
	right: 0;
	height: 30rpx;
	line-height: 30rpx;
	padding: 0 8rpx;
	min-width: 30rpx;
	text-align: center;
}
.product-con .product-intro .conter view {
  width: 100% !important;
}
.product-con .product-intro{
	margin-top: 0!important;
}
.product-con .product-intro .title{
	background: #f5f5f5!important;
}
</style>
