<template>
  <view class="my-order" ref="container">
    <!-- <view class="header bg-color-red"> -->
<!--      <view class="picTxt acea-row row-between-wrapper">
        <view class="text">
          <view class="name">订单信息</view>
          <view>累计订单：{{ orderData.orderCount || 0 }} 总消费：￥{{orderData.sumPrice || 0 }}</view>
        </view>
      </view> -->
    <!-- </view> -->
    <view class="nav acea-row row-around">
      <view class="item" :class="{ on: type === 20 }" @click="changeType(20)">
        <span>全部</span>
      </view>		
      <view class="item" :class="{ on: type === 0 }" @click="changeType(0)">
        <span>待支付</span>
        <!-- <view class="num">{{ orderData.unpaidCount || 0 }}</view> -->
      </view>
      <view class="item" :class="{ on: type === 1 }" @click="changeType(1)">
        <span>待发货</span>
        <!-- <view class="num">{{ orderData.unshippedCount || 0 }}</view> -->
      </view>
      <view class="item" :class="{ on: type === 2 }" @click="changeType(2)">
        <span>待收货</span>
        <!-- <view class="num">{{ orderData.receivedCount || 0 }}</view> -->
      </view>
      <view class="item" :class="{ on: type === 3 }" @click="changeType(3)">
        <span>已完成</span>
      </view>
      <!-- <view class="item" :class="{ on: type === 3 }" @click="changeType(4)">
        <span>已完成</span>
        <view class="num">{{ orderData.completeCount || 0 }}</view>
      </view> -->
    </view>
    <view class="list">
      <view class="item" v-for="(order,orderListIndex) in orderList" :key="orderListIndex">
        <view class="title acea-row row-between-wrapper">
          <view class="acea-row row-middle">
<!--            <span
              class="sign cart-color acea-row row-center-wrapper"
              v-if="order.combinationId > 0"
            >拼团</span> -->
            <!-- <span class="sign cart-color acea-row row-center-wrapper" v-if="order.seckillId > 0">秒杀</span>
            <span class="sign cart-color acea-row row-center-wrapper" v-if="order.bargainId > 0">砍价</span> -->
            <!-- <span class="sign cart-color acea-row row-center-wrapper" v-if="order.storeId > 0">门店</span> -->
            <span class="sign2">沃链商城</span>
			<!-- {{ order.createTime }} -->
          </view>
          <view class="font-color-red">{{ getStatus(order) }}</view>
        </view>
        <view @click="goOrderDetails(order)">
          <view
            class="item-info acea-row row-between row-top"
            v-for="(cart,cartInfoIndex) in order.cartInfo"
            :key="cartInfoIndex"
          >
            <view class="pictrue">
              <image
                :src="cart.productInfo.image"
                @click.stop="
                  $yrouter.push({ path: '/pages/shop/GoodsCon/index',query:{id:cart.productInfo.id}  })
                "
                v-if="cart.combinationId === 0 && cart.bargainId === 0 &&cart.seckillId === 0"
              />
              <image
                :src="cart.productInfo.image"
                @click.stop="
                  $yrouter.push({
                    path: '/pages/activity/GroupDetails/index',query:{id:cart.combinationId}
                  })
                "
                v-else-if="cart.combinationId > 0"
              />
              <image
                :src="cart.productInfo.image"
                @click.stop="
                  $yrouter.push({
                    path: '/pages/activity/DargainDetails/index',query:{id:cart.bargainId}
                  })
                "
                v-else-if="cart.bargainId > 0"
              />
              <image
                :src="cart.productInfo.image"
                @click.stop="
                  $yrouter.push({
                    path: '/pages/activity/SeckillDetails/index',query:{id:cart.seckillId}
                  })
                "
                v-else-if="cart.seckillId > 0"
              />
            </view>
            <view class="text acea-row row-between">
              <view class="name line2">{{ cart.productInfo.storeName }}</view>
			  <view class="totalPrice">
			    <!-- 共{{ order.cartInfo.length || 0 }}件商品，总金额 --> 实付:
			    ￥{{ order.payPrice }}+ <text class="money font-color-red">{{(cart.productInfo.otPrice-cart.productInfo.price)*cart.cartNum }} </text>  (补贴券)
			  </view>
			  <view class="arrow-jt">
			    <!-- <view>
			      ￥{{
			      cart.productInfo.attrInfo
			      ? cart.productInfo.attrInfo.price
			      : cart.productInfo.price
			      }}
			    </view> -->
			    <!-- <view>x{{ cart.cartNum }}</view> -->
			  	   <text class="iconfont icon-jiantou"></text>
			  </view>
            </view>
          </view>
        </view>
        <view class="bottom acea-row row-right row-middle">
          <template v-if="order._status._type == 0">
            <view class="bnt bg-color-quxiaodingdan" @click="cancelOrder(order)">取消订单</view>
            <view class="bnt bg-color-lijifukuan" @click="goOrderDetails(order)">立即付款</view>
          </template>
		  <template v-if="order._status._type == 1">
			  <view class="bnt bg-color-tixingfahuo" @click="goOrderDetails(order)">查看详情</view>
		    <view class="bnt bg-color-tixingfahuo" @click="remindShipment(order)">提醒发货</view> 
		  </template>
         <!-- <template v-if="order._status._type == 1 || order._status._type == 9">
            <view class="bnt bg-color-tixingfahuo" @click="goOrderDetails(order)">查看详情</view>
          </template> -->
          <template v-if="order._status._type == 2">
			<view class="bnt bg-color-querenshouhuo" @click="buyAgain(order)">再次购买</view> 
            <view class="bnt default" @click="goLogistics(order)">查看物流</view>
            <view class="bnt bg-color-querenshouhuo" @click="takeOrder(order)">确认收货</view>
          </template>
          <template v-if="order._status._type == 3">
            <!--<view-->
            <!--class="bnt default"-->
            <!--@click="-->
            <!--$yrouter.push({ path: '/pages/order/Logistics/index',query:{id:order.orderId}})-->
            <!--"-->
            <!--v-if="order.deliveryType == 'express'"-->
            <!--&gt;-->
            <!--查看物流-->
            <!--</view>-->
			<view class="img">
				<image src="../../../static/yiwancheng.png"></image>
			</view>
            <!-- <view class="bn·t bg-color-qupingjia" @click="goOrderDetails(order)">评价</view> -->
			<view class="bnt bg-color-chakanOrder" @click="goOrderDetails(order)">查看订单</view>
			<view class="bnt bg-color-querenshouhuo" @click="buyAgain(order)">再次购买</view> 
			<!-- <view class="bnt bg-color-querenshouhuo" @click="goShopGoodsCon(order)">进入到商品详情</view> -->
          </template>
          <template v-if="order._status._type == 4">
			  <view class="img">
				  <image src="../../../static/yiwancheng.png"></image>
			  </view>
			  <view class="bnt bg-color-querenshouhuo" @click="buyAgain(order)">再次购买</view> 
            <view class="bnt bg-color-chakanOrder" @click="goOrderDetails(order)">查看订单</view>
          </template>
          <template v-if="order._status._type == -1 || order._status._type == -2">
            <view class="bnt bg-color-chakanOrder" @click="goOrderDetails(order)">查看详情</view>
          </template>		  
        </view>
      </view>
    </view>
    <view class="noCart" v-if="orderList.length === 0 && page > 1">
      <view class="pictrue">
        <image src="@/static/images/noOrder.png" />
      </view>
    </view>
    <Loading :loaded="loaded" :loading="loading"></Loading>
    <Payment v-model="pay" :types="payType" @checked="toPay" :balance="userInfo.nowMoney"></Payment>
  </view>
</template>
<script>
import { getOrderData, getOrderList,remindShip } from "@/api/order";
import {postCartAdd} from "@/api/store";
import {
  cancelOrderHandle,
  payOrderHandle,
  takeOrderHandle
} from "@/libs/order";
import Loading from "@/components/Loading";
import Payment from "@/components/Payment";
import DataFormat from "@/components/DataFormat";
import { mapGetters } from "vuex";
import { isWeixin, dataFormat } from "@/utils";

const STATUS = [
  "待付款",
  "已付款",
  "待收货",
  "待评价",
  "已完成",
  "申请退款中",
  "退款成功",
  "",
  "",
  "",
  "",
  "待付款"
];

const NAME = "MyOrder";

export default {
  name: NAME,
  data() {
    return {
      offlinePayStatus: 2,
      orderData: {},
      type: "",
      page: 1,
      limit: 20,
      loaded: false,
      loading: false,
      orderList: [],
      pay: false,
      payType: ["yue", "weixin"],
      from: this.$deviceType
    };
  },
  components: {
    Loading,
    Payment,
    DataFormat
  },
  computed: mapGetters(["userInfo"]),
  onShow: function() {
    console.log(this);
    this.type = parseInt(this.$yroute.query.type) || 0;
    this.changeType(this.type);
    this.getOrderData();
    this.getOrderList();
  },
  onHide: function() {
    this.orderList = [];
    this.page = 1;
    this.limit = 20;
    this.loaded = false;
    this.loading = false;
  },
  methods: {
    goShopGoodsCon(order) {
      this.$yrouter.push({
        path: "/pages/shop/GoodsCon/index",
        query: { id: order.orderId }
      });
    },	  
    goLogistics(order) {
      this.$yrouter.push({
        path: "/pages/order/Logistics/index",
        query: { id: order.orderId }
      });
    },
	//再次购买按钮
	buyAgain(order){
		let that =this;
		let  list = order.cartInfo;
		list.forEach(function(item){
		    console.log(item)
			let q = {
				combinationId:0,
				secKillId:0,
				bargainId:0,
			    productId: item.productId,
			    cartNum: item.cartNum,
			    new: 0,
			    uniqueId:item.productAttrUnique,
			 };
		postCartAdd(q).then(function(res) {
			that.$yrouter.switchTab("/pages/shop/ShoppingCart/index")
		  })
		  .catch(error => {
		    uni.showToast({
		      title: error.data.msg,
		      icon: "none",
		      duration: 2000
		    });
		  });	
		});
	},
    goOrderDetails(order) {
      this.$yrouter.push({
        path: "/pages/order/OrderDetails/index",
        query: { id: order.orderId }
      });
    },
	// 提醒发货
	remindShipment(order){
		console.log(order.orderId)
		remindShip(order.orderId).then(res => {
			console.log(res)
		       uni.showToast({
		         title: res.data,
		         icon: "none",
		         duration: 2000
		       });
		       return;
		})
		.catch(res => {
           uni.showToast({
                title: res.msg,
                icon: "none",
                duration: 2000
            });
        });
	},
    dataFormat,
    setOfflinePayStatus: function(status) {
      var that = this;
      that.offlinePayStatus = status;
      if (status === 1) {
        if (that.payType.indexOf("offline") < 0) {
          that.payType.push("offline");
        }
      }
    },
    getOrderData() {
      getOrderData().then(res => {
        this.orderData = res.data;
      });
    },
    takeOrder(order) {
      takeOrderHandle(order.orderId).finally(() => {
        this.reload();
        this.getOrderData();
      });
    },
    reload() {
      this.changeType(this.type);
    },
    changeType(type) {
      this.type = type;
      this.orderList = [];
      this.page = 1;
      this.loaded = false;
      this.loading = false;
      this.getOrderList();
    },
    getOrderList() {
      if (this.loading || this.loaded) return;
      this.loading = true;
      const { page, limit, type } = this;
      getOrderList({
        page,
        limit,
        type
      }).then(res => {
		console.log(res.data.length)
        this.orderList = this.orderList.concat(res.data);
        this.page++;
        this.loaded = res.data.length < this.limit;
        this.loading = false;
      });
    },
    getStatus(order) {
		let status=order._status._type;
		if(status==3){
			status=4;
		}
		if(status==-1){
			status=5;
		}else if(status==-2){
			status=6;
		}
      return STATUS[status];
    },
    cancelOrder(order) {
      cancelOrderHandle(order.orderId)
        .then(() => {
          this.getOrderData();
          this.orderList.splice(this.orderList.indexOf(order), 1);
        })
        .catch(() => {
          this.reload();
        });
    },
    paymentTap: function(order) {
      var that = this;
      if (
        !(order.combinationId > 0 || order.bargainId > 0 || order.seckillId > 0)
      ) {
        that.setOfflinePayStatus(order.offlinePayStatus);
      }
      this.pay = true;
      this.toPay = type => {
        payOrderHandle(order.orderId, type, that.from)
          .then(() => {
            const type = parseInt(this.$yroute.query.type) || 0;
            that.changeType(type);
            that.getOrderData();
          })
          .catch(() => {
            const type = parseInt(that.$yroute.query.type) || 0;
            that.changeType(type);
            that.getOrderData();
          });
      };
    },
    toPay() {}
  },
  mounted() {},
  onReachBottom() {
    !this.loading && this.getOrderList();
  }
};
</script>

<style scoped lang="less">
	.my-order{
	    background: #F2F2F2;
	}
	/* 立即付款style */
	.bg-color-lijifukuan{
	   background:linear-gradient(150deg,rgba(255,67,12,1) 0%,rgba(211,2,2,1) 100%);
	   color: #FFFFFF;
	}
	
	/* 提醒发货style   取消订单style   查看物流style.......*/
	.bg-color-tixingfahuo, .bg-color-quxiaodingdan, .default,
	.bg-color-qupingjia,.bg-color-chakanOrder{
	    color: #292929;
	    border: 0.02* 100rpx solid #A9A9A9;
	}
	.bg-color-querenshouhuo{
	   color: #D30202;
	   border:0.02* 100rpx solid #D30202;
	}
.noCart {
  margin-top: 0.17 * 100rpx;
  padding-top: 0.1 * 100rpx;
}

.noCart .pictrue {
  width: 4 * 100rpx;
  height: 3 * 100rpx;
  overflow: hidden;
  margin: 0.7 * 100rpx auto 0.5 * 100rpx auto;
}

.noCart .pictrue image {
  width: 4 * 100rpx;
  height: 3 * 100rpx;
}
</style>
