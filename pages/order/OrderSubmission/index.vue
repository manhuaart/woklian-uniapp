<template>
  <view class="order-submission">
    <view class="allAddress" :style="systemStore ? '' : 'padding-top: 0.2*100rpx'">
      <!-- <view class="nav acea-row"> -->
<!--        <view
          class="item font-color-red"
          :class="shipping_type === 0 ? 'on' : 'on2'"
          @click="addressType(0)"
          v-if="systemStore"
        ></view> -->
<!--        <view
          class="item font-color-red"
          :class="shipping_type === 1 ? 'on' : 'on2'"
          @click="addressType(1)"
          v-if="systemStore"
        ></view> -->
      <!-- </view> -->
      <view
        class="address acea-row row-between-wrapper"
        v-if="shipping_type === 0"
        @click="addressTap"
      >
        <view class="addressCon" v-if="addressInfo.realName">
          <view class="name">
			 <view class="img-address"><image src="../../../static/images/weizhi.png"></image></view>
            <text class="realName">{{ addressInfo.realName }}</text>
            <text class="phone">{{ addressInfo.phone }}</text>
          </view>
          <view class="dizhi">
            <text class="default font-color-red" v-if="addressInfo.isDefault">[默认]</text>
            {{ addressInfo.province }}{{ addressInfo.city}}{{ addressInfo.district }}{{ addressInfo.detail }}
          </view>
        </view>
        <view class="addressCon" v-else>
          <view class="setaddress">
			  <view class="img-address"><image src="../../../static/images/weizhi.png"></image></view>
			  <view class="text">请添加收货地址</view>
		  </view>
        </view>
        <view class="iconfont icon-jiantou"></view>
      </view>
      <div class="address acea-row row-between-wrapper" v-else @click="showStoreList">
        <div class="addressCon">
          <div class="name">
            {{ storeItems.name || systemStore.name }}
            <span
              class="phone"
            >{{storeItems.phone || systemStore.phone}}</span>
          </div>
          <div>{{ storeItems.address || systemStore.address }}</div>
        </div>
        <div class="iconfont icon-jiantou"></div>
      </div>
    </view>
	<view class="center">
		<view class="center-goods" :evaluate="0">
		  <view class="item acea-row row-between-wrapper" v-for="cart in cartInfo" :key="cart.id">
		    <view class="pictrue">
		      <image :src="cart.productInfo.image" class="image" />
		    </view>
		    <view class="text">
		      <view class="acea-row row-between-wrapper">
		        <view class="name line1">{{ cart.productInfo.storeName }}</view>
		        <!-- <view class="num">x {{ cart.cartNum }}</view> -->
		      </view>
		      <span
		        class="attr"
		        v-if="cart.productInfo.attrInfo"
		      >
		      {{ cart.productInfo.attrInfo.sku }}
			  </span>
			  <view class="btq">
			     <span class="bt">
			   	<img src="../../../static/imgs/jin.png" class="imgw" />可用补贴券{{ cart.productInfo.otPrice -cart.productInfo.price }}
			     </span>
			  </view>
			  <view class="dsjia">
				   <view class="lef"> ￥<span>{{ cart.truePrice }}</span><text>到手价</text></view>
				   <view class="num">x{{ cart.cartNum }}</view>
			  </view>  
		      <view class="money">原价 <span>￥ <text>{{cart.productInfo.otPrice }}</text></span></view>
		      <view class="evaluate" v-if="evaluate == 3" @click="routerGo(cart)">评价</view>
		    </view>
		  </view>
		</view>	
		<!-- <OrderGoods :evaluate="0" :cartInfo="orderGroupInfo.cartInfo"></OrderGoods> -->
		    <view class="wrapper">
			 <view class="item acea-row row-between-wrapper">
				 <view>订单备注</view>
				 <view class="input"><input v-model="mark" placeholder="建议留言前先与商家沟通确认" maxlength="50"></input></view>
			 </view>
			 <view class="item acea-row row-between-wrapper" v-if="deduction === false">
		      <!-- <view class="item acea-row row-between-wrapper" @click="couponTap" v-if="deduction === false"> -->
				<view>优惠券</view>
		        <view class="discount">
					 暂无可用
		          <!-- {{ usableCoupon.couponTitle || "选择" }}
		          <text class="iconfont icon-jiantou"></text> -->
		        </view>
		      </view>
		      <!-- <view
		        class="item acea-row row-between-wrapper"
		        v-if="deduction === false && enableIntegral === true"
		      >
		        <view>积分抵扣</view>
		        <view class="discount">
		          <view class="select-btn">
		            <view class="checkbox-wrapper"> -->
		              <!-- <input type="checkbox" v-model="useIntegral" @click="changeUseIntegral"/> -->
		              <!-- <checkbox-group @change="changeUseIntegral">
		                <label class="well-check">
		                  <text class="integral">
		                    当前积分
		                    <text class="num font-color-red">{{ userInfo.integral || 0 }}</text>
		                  </text>
		                  <checkbox value="true" :checked="useIntegral ? true : false"></checkbox>
		                </label>
		              </checkbox-group> -->
		            <!-- </view>
		          </view>
		        </view>
		      </view> -->
			  <view class="item acea-row row-between-wrapper">
			    <view>购买方式</view>
			    <view class="discount">
			          微信+补贴券
			    </view>
			  </view>	  
			  <view class="item acea-row row-between-wrapper" style="align-items: stretch;">
			    <view>补贴券</view>
			    <view class="discount" v-if="orderGroupInfo.couponAmount">
					<text class="quan1">-{{orderGroupInfo.couponAmount}}</text>
					<text class="quan2">/{{usableCoupon.couponGrantNum}}</text>
					<text class="quan3">(可用{{usableCoupon.couponGrantNum}}补贴券抵扣{{orderGroupInfo.couponAmount}}元)</text>
				</view>
			  </view>
		      <view class="item acea-row row-between-wrapper" v-if="shipping_type === 0">
		        <view>快递费用</view>
		        <view class="discount">
		          {{
		          orderGroupInfo.priceGroup.storePostage > 0
		          ? orderGroupInfo.priceGroup.storePostage
		          : "免运费"
		          }}
		        </view>
		      </view>
              <view class="item acea-row row-between-wrapper">
		      </view>			  
		<!--      <view v-else>
		        <view class="item acea-row row-between-wrapper">
		          <view>联系人</view>
		          <view class="discount">
		            <input type="text" placeholder="请填写您的联系姓名" v-model="contacts" />
		          </view>
		        </view>
		        <view class="item acea-row row-between-wrapper">
		          <view>联系电话</view>
		          <view class="discount">
		            <input type="text" placeholder="请填写您的联系电话" v-model="contactsTel" />
		          </view>
		        </view>
		      </view> -->
		      
		    </view>
		    <!-- <view class="wrapper"> -->
		      <!-- <view class="item"> -->
		        <!-- <view>支付方式</view> -->
		        <!-- <view class="list"> -->
		          <!-- <view
		            class="payItem acea-row row-middle"
		            :class="active === 'weixin' ? 'on' : ''"
		            @click="payItem('weixin')"
		            v-show="isWeixin"
		          >
		            <view class="name acea-row row-center-wrapper">
		              <view class="iconfont icon-weixin2" :class="active === 'weixin' ? 'bounceIn' : ''"></view>微信支付
		            </view>
		            <view class="tip">微信快捷支付</view>
		          </view> -->
		          <!-- <view
		            class="payItem acea-row row-middle"
		            :class="active === 'weixin' ? 'on' : ''"
		            @click="payItem('weixin')"
		            v-show="!isWeixin"
		          >
		            <view class="name acea-row row-center-wrapper">
		              <view class="iconfont icon-weixin2" :class="active === 'weixin' ? 'bounceIn' : ''"></view>微信支付
		            </view>
		            <view class="tip">微信快捷支付</view>
		          </view> -->
		          <!-- <view
		            class="payItem acea-row row-middle"
		            :class="active === 'yue' ? 'on' : ''"
		            @click="payItem('yue')"
		          >
		            <view class="name acea-row row-center-wrapper">
		              <view class="iconfont icon-icon-test" :class="active === 'yue' ? 'bounceIn' : ''"></view>余额支付
		            </view>
		            <view class="tip">可用余额：{{ userInfo.nowMoney || 0 }}</view>
		          </view> -->
		        <!-- </view> -->
		      <!-- </view> -->
		    <!-- </view> -->
		    <!-- <view class="moneyList"> -->
		      <!-- <view class="item acea-row row-between-wrapper" v-if="orderPrice.totalPrice !== undefined">
		        <view>商品总价：</view>
		        <view class="money">￥{{ orderPrice.totalPrice }}</view>
		      </view> -->
		      <!-- <view class="item acea-row row-between-wrapper" v-if="orderPrice.payPostage > 0">
		        <view>运费：</view>
		        <view class="money">￥{{ orderPrice.payPostage }}</view>
		      </view> -->
		      <!-- <view class="item acea-row row-between-wrapper" v-if="orderPrice.couponPrice > 0">
		        <view>优惠券抵扣：</view>
		        <view class="money">-￥{{ orderPrice.couponPrice }}</view>
		      </view> -->
		<!--      <view class="item acea-row row-between-wrapper" v-if="orderPrice.deductionPrice > 0">
		        <view>积分抵扣：</view>
		        <view class="money">-￥{{ orderPrice.deductionPrice }}</view>
		      </view> -->
		    <!-- </view> -->
	</view>
    <view class="footer acea-row row-between-wrapper">
      <view>
        <!-- 合计 -->   
        <view class="weight" v-if="orderPrice.payPrice">￥<span>{{orderPrice.payPrice}}</span><text v-if="orderGroupInfo.couponAmount">( 补贴券{{orderGroupInfo.couponAmount}} )</text></view>
      </view>
      <view class="settlement" @click="createOrder">提交订单</view>
    </view>
	<view class="maskcenter" v-if="phMask">
	  <view class="peoCon">
		  <view class="bonText">绑定手机号</view>
		  <view class="peoConbox">
			  <input class="weui-input" type="number" maxlength="11" placeholder="请输入您的手机号" v-model="newPhone" />
		  </view>
		  <view class="peoConbox">
				<input class="weui-input" type="number" maxlength="6" placeholder="请输入验证码" v-model="newYzm"  />
				<text class="getYzm" @click="sendMessage()">{{verifyInfo}}</text>
		  </view>
		  <view class="userBtn">
			  <text @click="cancelMask()">取消</text>
			  <text @click="bandPhone()">绑定</text>
		  </view>
	  </view>
	</view>
    <CouponListWindow
      v-on:couponchange="changecoupon($event)"
      v-model="showCoupon"
      :price="orderPrice.totalPrice"
      :checked="usableCoupon.id"
      @checked="changeCoupon"
      :cartid="cartid"
    ></CouponListWindow>
    <AddressWindow
      @checked="changeAddress"
      @redirect="addressRedirect"
      v-model="showAddress"
      :checked="addressInfo.id"
      ref="mychild"
    ></AddressWindow>
  </view>
</template>

<script>
import OrderGoods from "@/components/OrderGoods";
import CouponListWindow from "@/components/CouponListWindow";
import AddressWindow from "@/components/AddressWindow";
import {bandPhones,getYzm } from "@/api/user";
import { postOrderConfirm, postOrderComputed, createOrder } from "@/api/order";
import { mapGetters } from "vuex";
import { weappPay } from "@/libs/wechat";
import { isWeixin } from "@/utils";

const NAME = "OrderSubmission",
  _isWeixin = isWeixin();
export default {
  name: NAME,
  components: {
    OrderGoods,
    CouponListWindow,
    AddressWindow
  },
  props: {},
  data: function() {
    return {
      offlinePayStatus: 2,
      from: this.$deviceType,
      deduction: true,
      enableIntegral: true,
      enableIntegralNum: 0,
      isWeixin: _isWeixin,
      pinkId: 0,
      active: _isWeixin ? "weixin" : "yue",
      showCoupon: false,
      showAddress: false,
      addressInfo: {},
      couponId: 0,
      orderGroupInfo: {
        priceGroup: {}
      },
	  cartInfo:{},
      usableCoupon: {},
      addressLoaded: false,
      useIntegral: false,
      orderPrice: {
        payPrice: "计算中"
      },
      mark: "",
      systemStore: {},
      shipping_type: 0,
      contacts: "",
      contactsTel: "",
      storeSelfMention: 0,
      cartid: "",
	  newPhone: '',
	  newYzm: '',
	  isdisable: false,
	  verifyInfo: '获取验证码',
	  codeInput: '',
	  phMask: false,
    };
  },
  computed: mapGetters(["userInfo", "storeItems"]),
  watch: {
    useIntegral() {
      this.computedPrice();
    },
    $yroute(n) {
      if (n.name === NAME) this.getCartInfo();
    },
    shipping_type() {
      this.computedPrice();
    }
  },
  mounted: function() {
    let that = this;
    this.$store.dispatch("getUser", true);
    that.getCartInfo();
    console.log(that.$yroute);
    if (that.$yroute.query.pinkid !== undefined) {
      that.pinkId = that.$yroute.query.pinkid;
    }
    if (that.$yroute.query.id !== undefined) {
      that.cartid = that.$yroute.query.id;
      console.log(that.cartid)
    }
  },
  methods: {
	cancelMask(){
		var that=this
		that.phMask = !that.phMask
		that.newPhone = ''
		that.newYzm = ''
	},
	sendMessage() {
		var that = this
		var myreg=/^[1][3,4,5,7,8][0-9]{9}$/;
		if(!that.newPhone){
			uni.showToast({
			  title: "请输入手机号",
			  icon: "none",
			  duration: 2000
			});
			return
		}else if(!myreg.test(that.newPhone)){
			uni.showToast({
			  title: "请输入正确得手机号",
			  icon: "none",
			  duration: 2000
			});
			return
		}else{
			if (that.isdisable == false) {
				var count = 60;
				var timer = setInterval(function () {
					count--;
					if (count >= 1) {
						that.verifyInfo= count + 's'
					} else {
						that.verifyInfo= '重新发送'
						clearInterval(timer);
						that.isdisable = false;
					}
				}, 1000);
				that.isdisable = true;
				
				getYzm(that.newPhone)
				  .then(res => {
					  console.log(res)
					  if(res.status==200){
						  uni.showToast({
						    title: "验证码发送成功",
						    icon: "none",
						    duration: 2000
						  });
					  }else{
						  uni.showToast({
						    title: res.msg,
						    icon: "none",
						    duration: 2000
						  });
						  that.verifyInfo= '重新发送'
						  clearInterval(timer);
						  that.isdisable = false;
					  }
				    
				  })
				  .catch(error => {
						uni.showToast({
						  title: error.data.msg,
						  icon: "none",
						  duration: 2000
						});
						that.verifyInfo= '重新发送'
						clearInterval(timer);
						that.isdisable = false;
				  });
				
			}
		}
	},
	bandPhone(){
		var that=this
		bandPhones(that.newPhone, that.newYzm)
		  .then(res => {
			  if(res.status==200){
				  uni.showToast({
					title: "绑定成功",
					icon: "none",
					duration: 2000
				  });
				  that.$store.dispatch("userInfo", true);
				  that.cancelMask();
			  }else{
				  uni.showToast({
					title: '发送失败',
					icon: "none",
					duration: 2000
				  });
			  }
		  })
		  .catch(error => {
				
		  });
	},
    showStoreList() {
      this.$store.commit("get_to", "orders");
      this.$yrouter.push({
        path: "/pages/shop/StoreList/index"
      });
    },
    changeUseIntegral: function(e) {
      // this.computedPrice();
      this.useIntegral = e.mp.detail.value[0];
    },
    computedPrice() {
      let shipping_type = this.shipping_type;
      postOrderComputed(this.orderGroupInfo.orderKey, {
        addressId: this.addressInfo.id,
        useIntegral: this.useIntegral ? 1 : 0,
        couponId: this.usableCoupon.id || 0,
        shipping_type: parseInt(shipping_type) + 1
      }).then(res => {
        const data = res.data;
        if (data.status === "EXTEND_ORDER") {
          this.$yrouter.replace({
            path: "/pages/order/OrderDetails/index",
            query: {
              id: data.result.orderId
            }
          });
        } else {
          this.orderPrice = data.result;
        }
      });
    },
    getCartInfo() {
      const cartIds = this.$yroute.query.id;
      if (!cartIds) {
        uni.showToast({
          title: "参数有误",
          icon: "none",
          duration: 2000
        });
        return this.$yrouter.back();
      }
      postOrderConfirm(cartIds)
        .then(res => {
			console.log(res.data.addressInfo)
          this.offlinePayStatus = res.data.offline_pay_status;
          this.orderGroupInfo = res.data;
		  this.cartInfo =  res.data.cartInfo;
          this.deduction = res.data.deduction;
          this.usableCoupon = res.data.usableCoupon || {};
          this.addressInfo = res.data.addressInfo || {};
          this.systemStore = res.data.systemStore || {};
          this.storeSelfMention = res.data.storeSelfMention;
          this.computedPrice();
        })
        .catch(() => {
          uni.showToast({
            title: "加载订单数据失败",
            icon: "none",
            duration: 2000
          });
        });
    },
    addressTap: function() {
      this.showAddress = true;
      if (!this.addressLoaded) {
        this.addressLoaded = true;
        this.$refs.mychild.getAddressList();
      }
    },
    addressRedirect() {
      this.addressLoaded = false;
      this.showAddress = false;
    },
    couponTap: function() {
      this.showCoupon = true;
    },
    changeCoupon: function(coupon) {
      if (!coupon) {
        this.usableCoupon = {
          couponTitle: "不使用优惠券",
          id: 0
        };
      } else {
        this.usableCoupon = coupon;
      }
      this.computedPrice();
    },
    payItem: function(index) {
		console.log(index)
      this.active = index;
    },
    changeAddress(addressInfo) {
      this.addressInfo = addressInfo;
    },
    createOrder() {
      let shipping_type = this.shipping_type;
	  this.active = "weixin";
      if (!this.active) {
        uni.showToast({
          title: "请选择支付方式",
          icon: "none",
          duration: 2000
        });
        return;
      }
      if (!this.addressInfo.id && !this.shipping_type) {
        uni.showToast({
          title: "请选择收货地址",
          icon: "none",
          duration: 2000
        });
        return;
      }
      if (this.usableCoupon.couponGrantNum<this.orderGroupInfo.couponAmount){
          uni.showToast({
            title: "补贴券数量不足,不能抵扣该商品",
            icon: "none",
            duration: 2000
          });
          return;
      }
      if (this.shipping_type) {
        if (
          (this.contacts === "" || this.contactsTel === "") &&
          this.shipping_type
        ) {
          uni.showToast({
            title: "请填写联系人或联系人电话",
            icon: "none",
            duration: 2000
          });
          return;
        }

        if (!/^1(3|4|5|7|8|9|6)\d{9}$/.test(this.contactsTel)) {
          uni.showToast({
            title: "请填写正确的手机号",
            icon: "none",
            duration: 2000
          });
          return;
        }
        if (!/^[\u4e00-\u9fa5\w]{2,16}$/.test(this.contacts)) {
          uni.showToast({
            title: "请填写您的真实姓名",
            icon: "none",
            duration: 2000
          });
          return;
        }
      }
			
      uni.showLoading({
        title: "生成订单中"
      });
      let from = {};
      if (this.$deviceType == "app") {
        from.from = "app";
      }
      console.log(this.orderPrice);
      createOrder(this.orderGroupInfo.orderKey, {
        realName: this.contacts,
        phone: this.contactsTel,
        addressId: this.addressInfo.id,
        useIntegral: this.useIntegral ? 1 : 0,
        couponId: this.usableCoupon.id || 0,
		useCouponNum:this.orderPrice.useCouponNum,
        payType: this.active,
        pinkId: this.pinkId,
        seckillId: this.orderGroupInfo.seckill_id,
        combinationId: this.orderGroupInfo.combination_id,
        bargainId: this.orderGroupInfo.bargain_id,
        from: this.from,
        mark: this.mark || "",
        shippingType: parseInt(shipping_type) + 1,
        storeId: this.storeItems ? this.storeItems.id : this.systemStore.id,
        ...from
      })
        .then(res => {
          uni.hideLoading();
          const data = res.data;
		  if(data.status==-1){
			  uni.showToast({
			    title: data.msg,
			    icon: "none",
			    duration: 2000
			  });
			  this.phMask = !this.phMask
		  }else{
			  switch (data.status) {
			    case "ORDER_EXIST":
			    case "EXTEND_ORDER":
			      uni.showToast({
			        title: res.msg,
			        icon: "none",
			        duration: 2000
			      });
			      this.$yrouter.replace({
			        path: "/pages/order/OrderDetails/index",
			        query: {
			          id: data.result.orderId
			        }
			      });
			      break;
			    case "PAY_DEFICIENCY":
			      break;
			  
			    case "PAY_ERROR":
			      uni.showToast({
			        title: res.msg,
			        icon: "none",
			        duration: 2000
			      });
			      this.$yrouter.replace({
			        path: "/pages/order/OrderDetails/index",
			        query: {
			          id: data.result.orderId
			        }
			      });
			      break;
			    case "SUCCESS":
			      uni.showToast({
			        title: res.msg,
			        icon: "none",
			        duration: 2000
			      });
			      this.$yrouter.replace({
			        path: "/pages/order/OrderDetails/index",
			        query: {
			          id: data.result.orderId
			        }
			      });
			      break;
			    case "WECHAT_H5_PAY":
			      // H5支付
			      this.$yrouter.replace({
			        path: "/pages/order/OrderDetails/index",
			        query: {
			          id: data.result.orderId
			        }
			      });
			      setTimeout(() => {
			        // location.href = data.result.jsConfig.mweb_url;
			      }, 100);
			      break;
			    case "WECHAT_PAY":
			      // 小程序支付
			      weappPay(data.result.jsConfig).finally(() => {
			        this.$yrouter.replace({
			          path: "/pages/order/OrderDetails/index",
			          query: {
			            id: data.result.orderId
			          }
			        });
			      });
			      break;
			  
			    case "WECHAT_APP_PAY":
			      // APP支付
			      weappPay(data.result.jsConfig).finally(() => {
			        this.$yrouter.replace({
			          path: "/pages/order/OrderDetails/index",
			          query: {
			            id: data.result.orderId
			          }
			        });
			      });
			      break;
			    // 下面为原先微信支付方式，
			    // pay(data.result.jsConfig).finally(() => {
			    //   this.$yrouter.replace({
			    //     path: "/pages/order/OrderDetails/index" ,query: { id: data.result.orderId}
			    //   });
			    // });
			  }
		  }
          
        })
        .catch(err => {
          uni.hideLoading();
          uni.showToast({
            title:
              err.msg ||
              err.response.data.msg ||
              err.response.data.message ||
              "创建订单失败",
            icon: "none",
            duration: 2000
          });
        });
    }
  }
};
</script>
<style scoped lang="less">
.order-submission .wrapper .shipping select {
  color: #999;
  padding-right: 0.15 * 100rpx;
}

.order-submission .wrapper .shipping .iconfont {
  font-size: 0.3 * 100rpx;
  color: #515151;
}

.order-submission .allAddress {
  width: 100%;
  background-color:#F2F2F2;
  padding-top: 0.15 * 100rpx;
}
// .order-submission .allAddress .nav .item.on:before {
//   position: absolute;
//   bottom: 0;
//   content: "快递配送";
//   font-size: 0.28 * 100rpx;
//   display: block;
//   height: 0;
//   left: 0;
//   right: 0;
//   border-width: 0.4 * 100rpx;
//   border-style: solid;
//   border-color: #fff;
//   z-index: 9;
//   text-align: center;
//   line-height: 0.14 * 100rpx;
// }

// .order-submission .allAddress .nav .item:nth-of-type(2).on:before {
//   content: "到店自提";
//   border-width: 0.4 * 100rpx;
// }

// .order-submission .allAddress .nav .item.on2 {
//   position: relative;
// }

// .order-submission .allAddress .nav .item.on2:before {
//   position: absolute;
//   bottom: 0;
//   content: "到店自提";
//   font-size: 0.28 * 100rpx;
//   display: block;
//   height: 0;
//   left: 0;
//   right: 0;
//   border-width: 0.4 * 100rpx;
//   border-style: solid;
//   border-color: #d5e6e6;
//   text-align: center;
//   line-height: 0.14 * 100rpx;
// }

// .order-submission .allAddress .nav .item:nth-of-type(1).on2:before {
//   content: "快递配送";
//   border-width: 0.4 * 100rpx;
// }

.order-submission .allAddress .address {
  width: 6.86 * 100rpx;
  // height: 1.4 * 100rpx;
  border-radius:20rpx;
  margin: 0 auto;
  box-sizing: border-box;
}

// .order-submission .allAddress .line {
//   width: 7.1 * 100rpx;
//   margin: 0 auto;
// }

.order-submission .wrapper .item .discount input::placeholder {
  color: #ccc;
}
.maskcenter{
		width: 100%;
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
	.bonText{
		width: 100%;
		text-align: center;
		font-size: 32rpx;
		margin-bottom: 20rpx;
	}
	.userBtn{
		width: 100%;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-top: 40rpx;
	}
	.userBtn text{
		width: 45%;
		height: 70rpx;
		background: #e2e2e2;
		line-height: 70rpx;
		text-align: center;
		border-radius: 16rpx;
	}
	.userBtn text:nth-child(2){
		background: #D30202!important;
		color: #fff;
	}
	.peoCon{
		width: 500rpx;
		background: #fff;
		border-radius: 12rpx;
		padding: 40rpx;
	}
	.peoConbox{
		width: 500rpx;
		margin-bottom: 10rpx;
		display: flex;
		align-items: center;
		font-size: 24rpx;
		position: relative;
	}
	.peoConbox input{
		width: 100%;
		border-bottom:1px solid #f2f2f2;
		height: 80rpx;
	}
	.getYzm{
		position: absolute;
		right: 0;
		top: 20rpx;
		z-index: 99999;
		color: #D30202;
	}
	.peoConbox input.ipt{
		width: 80%;
		border-bottom:1px solid #f2f2f2;
		height: 60rpx;
	}
</style>

