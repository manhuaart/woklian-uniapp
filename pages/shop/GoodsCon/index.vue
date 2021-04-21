<template>
  <view :class="productConClass" style="position: relative;" class="bgGwc">
    <view v-if="storeInfo.id">
      <product-con-swiper :img-urls="storeInfo.sliderImageArr"></product-con-swiper>
	  <view  class="imgpoxCon">
	  	<image src="../../../static/imgs/fenxiang-4.png" mode="" @click="listenerActionSheet" class="imgPicboc"></image>
	  </view>
	  <view class="gwc">
	    <image src="../../../static/imgs/gwc.png" class="gwcimg" ></image>
	    <view class="boxc">
			<view>
			  <font>￥<text>{{ storeInfo.price }}</text>到手价</font>
			  <text class="line-line">原价￥{{ storeInfo.otPrice }}</text>
			</view>
			<view class="articles">
			  <image src="../../../static/imgs/gyj.png" class="gyj" ></image>
			  <font class='boxc-view-font'><text class='colotr'>可用补贴券</text> <text style="margin-right: 0; font-size: 36rpx;">{{storeInfo.otPrice - storeInfo.price}}</text></font>
			</view>
	    </view>
		
	  </view>
      <view class="wrapper">
        <view :class="{dian: !showCss}" class="introduce">
			{{ storeInfo.storeName }}
			<image v-if='shoppingName&& !btnshow' @click="showCon" src="../../../static/imgs/bottom.png" class="imgpic"></image>
			<image v-if='shoppingName &&btnshow' @click="showCon" src="../../../static/imgs/toop.png" class="imgpic"></image>
		</view>
        <view class="infopox">
		  <font>{{ storeInfo.keyword }}</font>
          <text>月销:{{ storeInfo.ficti }}{{ storeInfo.unitName }}</text>
        </view>
		<view class="attribute acea-row row-between-wrapper" @click="selecAttrTap">
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
        <view
          class="coupon acea-row row-between-wrapper"
          @click="couponTap"
          v-if="couponList.length"
        >
          <text class="hide line1 acea-row">
            <text>优惠券：</text>
            <text
              class="activity"
              v-for="(item, couponListEq) in couponList"
              :key="couponListEq"
            >满{{ item.use_min_price }}减{{ item.coupon_price }}</text>
          </text>
          <view class="iconfont icon-jiantou"></view>
        </view>
      </view>
      <!-- <view class="attribute acea-row row-between-wrapper">
        <view>
          运费：
          <text class="atterTxt">{{ tempName }}</text>
        </view>
      </view> -->

      
      <!-- <view class="store-info">
        <view class="title acea-row row-between-wrapper">
          <view>门店信息</view>
          <text @click="goStoreList()" class="praise">
            更多
            <text class="iconfont icon-jiantou"></text>
          </text>
        </view>
        <view class="info acea-row row-between-wrapper">
          <view class="picTxt acea-row row-between-wrapper">
            <view class="pictrue">
              <image :src="systemStore.image" />
            </view>
            <view class="text">
              <view class="name line1">{{ systemStore.name }}</view>
              <view class="address acea-row row-middle" @click="showChang(systemStore)">
                <text class="addressTxt">{{systemStore.address}}</text>
                <text class="iconfont icon-youjian"></text>
              </view>
            </view>
            <view class="addressBox">
              <a
                :href="'tel:'+systemStore.phone"
                class="iconfont icon-dadianhua01 font-color-red phone"
              ></a>
              <view class="addressTxt corlor-yshop">距离{{systemStore.distance}}千米</view>
            </view>
          </view>
        </view>
      </view> -->
      <view class="userEvaluation" v-if="replyCount">
        <view class="title acea-row row-between-wrapper">
          <view>评价({{ replyCount }})</view>
          <text @click="goEvaluateList(id)" class="praise">
            <!-- <text class="font-color-red">{{ replyChance }}%</text> -->查看全部
            <text class="iconfont icon-jiantou"></text>
          </text>
        </view>
        <user-evaluation :reply="reply"></user-evaluation>
      </view>
      <!-- <view class="superior">
        <view class="title acea-row row-center-wrapper">
          <image src="@/static/images/ling.png" />
          <text class="titleTxt">优品推荐</text>
          <image src="@/static/images/ling.png" />
        </view>
        <template>
          <view class="slider-banner banner">
            <swiper :options="swiperRecommend" v-if="goodList.length > 0">
              <swiper-slide v-for="(item, eq2) in goodList" :key="eq2">
                <view class="list acea-row row-middle">
                  <view class="item" v-for="val in item.list" :key="val.image">
                    <view class="pictrue">
                      <image :src="val.image" />
                    </view>
                    <view class="name line1">{{ val.store_name }}}</view>
                    <view class="money font-color-red">¥{{ val.price }}</view>
                  </view>
                </view>
              </swiper-slide>
              <view class="swiper-pagination" slot="pagination"></view>
            </swiper>
          </view>
        </template>
      </view> -->
      <view class="product-intro">
        <view class="title">商品详情</view>
        <view class="conter">
			<rich-text :nodes="storeInfo.description | formatRichText"></rich-text>
		</view>
      </view>
      <view style="height:100rpx;"></view>
      <view class="footer acea-row row-between-wrapper goin">
        <view class="item" @click="setCollect" v-if="storeInfo.userCollect">
          <view class="iconfont icon-shoucang1"></view>
          <text>收藏</text>
        </view>
        <view class="item" @click="setCollect" v-if="!storeInfo.userCollect">
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
          <view class="joinCart" @click="joinCart">
            <text>加入购物车</text>
          </view>
          <view class="buy" @click="tapBuy">
            <text>立即购买</text>
          </view>
        </view>
      </view>
      <CouponPop v-on:changeFun="changeFun" :coupon="coupon"></CouponPop>
      <ProductWindow v-on:changeFun="changeFun" :attr="attr" :cartNum="cart_num"></ProductWindow>
      <StorePoster
        v-on:setPosterImageStatus="setPosterImageStatus"
        :posterImageStatus="posterImageStatus"
        :posterData="posterData"
        :goodId="id"
      ></StorePoster>
      <ShareInfo v-on:setShareInfoStatus="setShareInfoStatus" :shareInfoStatus="shareInfoStatus"></ShareInfo>
      <view class="generate-posters acea-row row-middle on" v-if="posters">
        <view class="item" @click="setPosterImageStatus">
          <view class="iconfont icon-haibao"></view>
          <view>生成海报</view>
        </view>
      </view>
      <view class="generate-posters acea-row row-middle" v-if="!posters">
        <view class="item" @click="setPosterImageStatus">
          <view class="iconfont icon-haibao"></view>
          <view>生成海报</view>
        </view>
      </view>
      <view class="mask" @touchmove.prevent @click="listenerActionClose" v-show="posters"></view>
      <view class="posterCanvasWarp">
        <canvas class="posterCanvas" canvas-id="myCanvas"></canvas>
      </view>
    </view>
  </view>
</template>

<script>
// import { swiper, swiperSlide } from "vue-awesome-swiper";

import ProductConSwiper from "@/components/ProductConSwiper";
import UserEvaluation from "@/components/UserEvaluation";
import CouponPop from "@/components/CouponPop";
import ProductWindow from "@/components/ProductWindow";
import StorePoster from "@/components/StorePoster";
import ShareInfo from "@/components/ShareInfo";
import {
  getProductDetail,
  postCartAdd,
  getCartCount,
  getProductCode
} from "@/api/store";
import {
  getCoupon,
  getCollectAdd,
  getCollectDel,
  getUserInfo
} from "@/api/user";
import { isWeixin, PosterCanvas, handleQrCode } from "@/utils";
// import { wechatEvevt } from "@/libs/wechat";
import { imageBase64 } from "@/api/public";
import { mapGetters } from "vuex";

export default {
  name: "GoodsCon",
  components: {
    // swiper,
    // swiperSlide,
    ProductConSwiper,
    UserEvaluation,
    CouponPop,
    ProductWindow,
    StorePoster,
    ShareInfo
  },
  data: function() {
    return {
      shareInfoStatus: false,
      weixinStatus: false,
      mapShow: false,
	  btnshow: false,
      mapKey: "",
      posterData: {
        image: "",
        title: "",
        price: "",
        code: ""
      },
      posterImageStatus: false,
      animated: false,
      coupon: {
        coupon: false,
        list: []
      },
      attr: {
        cartAttr: false,
        productAttr: [],
        productSelect: {}
      },
      isOpen: false, //是否打开属性组件
      productValue: [],
      id: 0,
      storeInfo: {},
	  shoppingName: false,
	  showCss: false,
      couponList: [],
      attrTxt: "请选择",
      attrValue: "",
      cart_num: 1, //购买数量
      replyCount: "",
      replyChance: "",
      reply: [],
      priceName: 0,
      CartCount: 0,
      posters: false,
      banner: [{}, {}],
      swiperRecommend: {
        pagination: {
          el: ".swiper-pagination",
          clickable: true
        },
        autoplay: false,
        loop: false,
        speed: 1000,
        observer: true,
        observeParents: true
      },
      goodList: [],
      systemStore: {},
      qqmapsdk: null,
      productConClass: "product-con",
      tempName: "全国包邮"
    };
  },
  computed: mapGetters(["isLogin", "location"]),
  mounted: function() {
    let url = handleQrCode();
    if (url && url.productId) {
      this.id = url.productId;
    } else {
      this.id = this._route.query.id;
    }
    this.productCon();
  },
  watch: {
    posterImageStatus(status) {
      console.log(status);
      if (status) {
        this.productConClass = "noscroll product-con";
      } else {
        this.productConClass = "product-con";
      }
    }
  },
  filters: {
		/**
		 * 处理富文本里的图片宽度自适应
		 * 1.去掉img标签里的style、width、height属性
		 * 2.img标签添加style属性：max-width:100%;height:auto
		 * 3.修改所有style里的width属性为max-width:100%
		 * 4.去掉<br/>标签
		 * @param html
		 * @returns {void|string|*}
		 */
		formatRichText (html) { //控制小程序中图片大小
			if(html!=undefined){
				let newContent= html.replace(/<img[^>]*>/gi,function(match,capture){
					match = match.replace(/style="[^"]+"/gi, '').replace(/style='[^']+'/gi, '');
					match = match.replace(/width="[^"]+"/gi, '').replace(/width='[^']+'/gi, '');
					match = match.replace(/height="[^"]+"/gi, '').replace(/height='[^']+'/gi, '');
					return match;
				});
				newContent = newContent.replace(/style="[^"]+"/gi,function(match,capture){
					match = match.replace(/width:[^;]+;/gi, 'max-width:100%;').replace(/width:[^;]+;/gi, 'max-width:100%;');
					return match;
				});
				newContent = newContent.replace(/<br[^>]*\/>/gi, '');
				newContent = newContent.replace(/\<img/gi, '<img style="max-width:100%;height:auto;display:inline-block;margin:10rpx auto;"');
				return newContent;
			}
			
		}	
	},

  methods: {
	
	handleContact (e) {
		console.log(e.detail.path)
		console.log(e.detail.query)
	},
	showCon(){
		let that=this
		that.showCss = !that.showCss
		that.btnshow = !that.btnshow
	},
    goShoppingCart() {
      this.$yrouter.switchTab("/pages/shop/ShoppingCart/index");
    },
    goCustomerList() {
      this.$yrouter.push({
        path: "/pages/user/CustomerList/index"
      });
    },
    goStoreList() {
      this.$yrouter.push({
        path: "/pages/shop/StoreList/index"
      });
    },
    goEvaluateList(id) {
      this.$yrouter.push({
        path: "/pages/shop/EvaluateList/index",
        query: {
          id
        }
      });
    },
    showChang: function(data) {
      this.$yrouter.push({
        path: "/pages/map/index",
        query: data
      });
    },
    updateTitle() {
      // document.title = this.storeInfo.storeName || this.$yroute.meta.title;
    },
    setShareInfoStatus: function() {
      this.shareInfoStatus = !this.shareInfoStatus;
      this.posters = false;
    },
    shareCode: function() {
      var that = this;
      getProductCode(that.id).then(res => {
        that.posterData.code = res.data.code;
        that.listenerActionSheet();
      });
    },
    setPosterImageStatus: function() {
      this.posterImageStatus = !this.posterImageStatus;
      this.posters = false;
    },
    //产品详情接口；
    productCon: function() {
      let that = this;
      let from = this.location;
      if (this.$deviceType == "app") {
        from.from = "app";
      }
      uni.showLoading({ title: "加载中", mask: true });
      getProductDetail(that.id, from)
        .then(res => {
          res.data.storeInfo.description = res.data.storeInfo.description.replace(
            /\<img/gi,
            '<img style="max-width:100%;height:auto;display:block;"'
          );
		  
		  
		  
          that.$set(that, "storeInfo", res.data.storeInfo);
          // 给 attr 赋值，将请求回来的规格赋值给 attr
		  uni.setNavigationBarTitle({
		  　　title:res.data.storeInfo.storeName
		  })
          that.$set(that.attr, "productAttr", res.data.productAttr);
          that.$set(that, "productValue", res.data.productValue);
          that.$set(that, "replyCount", res.data.replyCount);
          that.$set(that, "replyChance", res.data.replyChance);
          that.reply = res.data.reply ? [res.data.reply] : [];
          that.$set(that, "reply", that.reply);
          that.$set(that, "priceName", res.data.priceName);
          that.$set(that, "tempName", res.data.tempName);
          that.posterData.image = that.storeInfo.image;

          if (that.storeInfo.storeName.length > 30) {
            that.posterData.title = that.storeInfo.storeName.substring(0, 30) + "...";
			that.shoppingName = true
          } else {
            that.posterData.title = that.storeInfo.storeName;
			that.shoppingName = false
          }
          that.posterData.price = that.storeInfo.price;
          that.posterData.code = that.storeInfo.codeBase;
          that.systemStore = res.data.systemStore;
          let good_list = res.data.goodList || [];
          let goodArray = [];
          let count = Math.ceil(good_list.length / 6);
          for (let i = 0; i < count; i++) {
            var list = good_list.slice(i * 6, 6);
            if (list.length)
              goodArray.push({
                list: list
              });
          }
          that.mapKay = res.data.mapKay;
          that.$set(that, "goodList", goodArray);
          that.updateTitle();
          that.DefaultSelect();
          that.getCartCount();
		  //足迹记录
		  res.data.storeInfo.right = 0;
		  var storeInfoHis = uni.getStorageSync('storeInfoHis');
		  storeInfoHis = storeInfoHis ? storeInfoHis : [];
		  storeInfoHis.unshift(res.data.storeInfo);
		  if(storeInfoHis.length>20){
			  storeInfoHis.pop();
		  }
		  uni.setStorageSync('storeInfoHis', storeInfoHis);
		  console.log(uni.getStorageSync('storeInfoHis'))
        })
        .catch(err => {
          uni.showToast({
            title:
              err.msg,
            icon: "none",
            duration: 2000
          });
        })
        .finally(() => {
          uni.hideLoading();
        });
    },
	
    //默认选中属性；
    DefaultSelect: function() {
      let productAttr = this.attr.productAttr;
      let value = [];
      for (let i = 0; i < productAttr.length; i++) {
        this.$set(productAttr[i], "index", 0);
        value.push(productAttr[i].attrValueArr[0]);
      }
      //sort();排序函数:数字-英文-汉字；
      let productSelect = this.productValue[value.sort().join(",")];
      if (productSelect && productAttr.length) {
        this.$set(
          this.attr.productSelect,
          "store_name",
          this.storeInfo.storeName
        );
        this.$set(this.attr.productSelect, "image", productSelect.image);
        this.$set(this.attr.productSelect, "price", productSelect.price);
		this.$set(this.attr.productSelect, "otPrice", productSelect.otPrice);
        this.$set(this.attr.productSelect, "stock", productSelect.stock);
        this.$set(this.attr.productSelect, "unique", productSelect.unique);
        this.$set(this.attr.productSelect, "cart_num", 1);
        this.$set(this, "attrValue", value.sort().join(","));
        this.$set(this, "attrTxt", "已选");
      } else if (!productSelect && productAttr.length) {
        this.$set(
          this.attr.productSelect,
          "store_name",
          this.storeInfo.storeName
        );
        this.$set(this.attr.productSelect, "image", this.storeInfo.image);
        this.$set(this.attr.productSelect, "price", this.storeInfo.price);
		this.$set(this.attr.productSelect, "otPrice", productSelect.otPrice);
        this.$set(this.attr.productSelect, "stock", 0);
        this.$set(this.attr.productSelect, "unique", "");
        this.$set(this.attr.productSelect, "cart_num", 0);
        this.$set(this, "attrValue", "");
        this.$set(this, "attrTxt", "请选择");
      } else if (!productSelect && !productAttr.length) {
        this.$set(
          this.attr.productSelect,
          "store_name",
          this.storeInfo.storeName
        );
        this.$set(this.attr.productSelect, "image", this.storeInfo.image);
        this.$set(this.attr.productSelect, "price", this.storeInfo.price);
		this.$set(this.attr.productSelect, "otPrice", productSelect.otPrice);
        this.$set(this.attr.productSelect, "stock", this.storeInfo.stock);
        this.$set(
          this.attr.productSelect,
          "unique",
          this.storeInfo.unique || ""
        );
        this.$set(this.attr.productSelect, "cart_num", 1);
        this.$set(this, "attrValue", "");
        this.$set(this, "attrTxt", "请选择");
      }
    },
    //购物车；
    ChangeCartNum: function(changeValue) {
      //changeValue:是否 加|减
      //获取当前变动属性
      let productSelect = this.productValue[this.attrValue];
      //如果没有属性,赋值给商品默认库存
      if (productSelect === undefined && !this.attr.productAttr.length) {
        productSelect = this.attr.productSelect;
      }
      //无属性值即库存为0；不存在加减；
      if (productSelect === undefined) return;
      let stock = productSelect.stock || 0;
      let num = this.attr.productSelect;
      if (changeValue) {
        num.cart_num++;
        if (num.cart_num > stock) {
          this.$set(this.attr.productSelect, "cart_num", stock);
          this.$set(this, "cart_num", stock);
        } else {
          this.$set(this.attr.productSelect, "cart_num", num.cart_num);
          this.$set(this, "cart_num", num.cart_num);
        }
      } else {
        num.cart_num--;
        if (num.cart_num < 1) {
          this.$set(this.attr.productSelect, "cart_num", 1);
          this.$set(this, "cart_num", 1);
        } else {
          this.$set(this.attr.productSelect, "cart_num", num.cart_num);
          this.$set(this, "cart_num", num.cart_num);
        }
      }
    },
    //将父级向子集多次传送的函数合二为一；
    changeFun: function(opt) {
      if (typeof opt !== "object") opt = {};
      let action = opt.action || "";
      let value = opt.value === undefined ? "" : opt.value;
      this[action] && this[action](value);
    },
    //打开优惠券插件；
    couponTap: function() {
      let that = this;
      that.coupons();
      that.coupon.coupon = true;
    },
    changecoupon: function(msg) {
      this.coupon.coupon = msg;
      this.coupons();
    },
    currentcoupon: function(res) {
      let that = this;
      that.coupon.coupon = false;
      that.$set(that.coupon.list[res], "is_use", true);
    },
    //可领取优惠券接口；
    coupons: function() {
      let that = this,
        q = {
          page: 1,
          limit: 20
        };
      getCoupon(q).then(res => {
        that.$set(that, "couponList", res.data || []);
        that.$set(that.coupon, "list", res.data);
      });
    },
    //打开属性插件；
    selecAttrTap: function() {
      this.attr.cartAttr = true;
      this.isOpen = true;
    },
    changeattr: function(msg) {
      // 修改了规格
      console.log(msg);
      this.attr.cartAttr = msg;
      this.isOpen = false;
    },
    //选择属性；
    ChangeAttr: function(res) {
      // 修改了规格
      console.log(res);
      let productSelect = this.productValue[res.value];
      if (productSelect) {
        this.attr.productAttr[res.indexw].index = res.indexn;
        this.$set(this.attr.productSelect, "image", productSelect.image);
        this.$set(this.attr.productSelect, "price", productSelect.price);
		this.$set(this.attr.productSelect, "otPrice", productSelect.otPrice);
        this.$set(this.attr.productSelect, "stock", productSelect.stock);
        this.$set(this.attr.productSelect, "unique", productSelect.unique);
        this.$set(this.attr.productSelect, "cart_num", 1);
        this.$set(this, "attrValue", res.value);
        this.$set(this, "attrTxt", "已选");
      } else {
        this.$set(this.attr.productSelect, "image", this.storeInfo.image);
        this.$set(this.attr.productSelect, "price", this.storeInfo.price);
		this.$set(this.attr.productSelect, "otPrice", productSelect.otPrice);
        this.$set(this.attr.productSelect, "stock", 0);
        this.$set(this.attr.productSelect, "unique", "");
        this.$set(this.attr.productSelect, "cart_num", 0);
        this.$set(this, "attrValue", "");
        this.$set(this, "attrTxt", "请选择");
      }
    },
    //收藏商品
    setCollect: function() {
      let that = this,
        id = that.storeInfo.id,
        category = "product";
      if (that.storeInfo.userCollect) {
        getCollectDel(id, category).then(function() {
          that.storeInfo.userCollect = !that.storeInfo.userCollect;
        });
      } else {
        getCollectAdd(id, category).then(function() {
          that.storeInfo.userCollect = !that.storeInfo.userCollect;
        });
      }
    },
    //  点击加入购物车按钮
    joinCart: function() {
      //0=加入购物车
      this.goCat(0);
    },
    // 加入购物车；
    goCat: function(news) {
      let that = this,
        productSelect = that.productValue[this.attrValue];
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
		secKillId:0,
		bargainId:0,
        productId: that.id,
        cartNum: that.attr.productSelect.cart_num,
        new: news,
        uniqueId:
          that.attr.productSelect !== undefined
            ? that.attr.productSelect.unique
            : ""
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
              complete: () => {
                that.getCartCount(true);
              }
            });
          }
        })
        .catch(error => {
          that.isOpen = false;
          uni.showToast({
            title: error.response.data.msg,
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
    //立即购买；
    tapBuy: function() {
      //  1=直接购买
      this.goCat(1);
    },
    listenerActionSheet: function() {
      if (isWeixin() === true) {
        this.weixinStatus = true;
      }
      this.posters = true;
    },
    listenerActionClose: function() {
      this.posters = false;
    }
  }
};
</script>


<style scoped lang="less">
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
rich-text {
  width: 750rpx;
  display: block;
}
rich-text img,
rich-text image {
  display: block;
  width: 100%;
}
</style>
