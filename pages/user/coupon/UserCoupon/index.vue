<template>
  <view ref="container">
    <!-- <div class="coupon-list" v-if="couponsList.length > 0">
      <div
        class="item acea-row row-center-wrapper"
        v-cloak
        v-for="(item, index) in couponsList"
        :key="index"
      >
        <div class="money" :class="item._type === 0 ? 'moneyGray' : ''">
          <div>
            ￥<span class="num">{{ item.couponPrice }}</span>
          </div>
          <div class="pic-num">满{{ item.useMinPrice }}元可用</div>
        </div>
        <div class="text">
          <div class="condition line1">
          {{ item.couponTitle }}
          </div>
          <div class="data acea-row row-between-wrapper">
            <div v-if="item.endTime === 0">不限时</div>
            <div v-else>{{ item.createTime }}-{{ item.endTime }}</div>
            <div class="bnt gray" v-if="item._type === 0">{{ item._msg }}</div>
            <div class="bnt bg-color-red" v-else>{{ item._msg }}</div>
          </div>
        </div>
      </div>
    </div> -->
	<view class="cousponbox" v-if="couponsList.length > 0">
		<view class="couspon" v-for="(item, index) in couponsList" :key="index">
			<image src="../../../../static/imgs/coupon.png" class="coubig" mode="" v-if="item._type==1"></image>
			<image src="../../../../static/imgs/coupon.png" class="coubigs" mode="" v-if="item._type==0"></image>
			<view class="boxcou">
				<view class="tcou">{{item.couponPrice}}元x{{item.couponGrantNum}}张</view>
				<view class="tcoupon" v-if="item._type==1">
					<text>{{ item.couponTitle }}</text>
					<text>有效期到{{ item.endTime }}</text>
				</view>
				<view class="tcoupon bgopc" v-if="item._type==0">
					<text>{{ item.couponTitle }}</text>
					<text>截止到{{ item.endTime }}(已过期)</text>
				</view>
			</view>
		</view>
	</view>
    <!--暂无优惠券-->
    <view
      class="noCommodity"
      v-if="couponsList.length === 0 && loading === true"
    >
      <view class="noPictrue">
        <image src="@/static/images/noCoupon.png" class="image" />
      </view>
    </view>
  </view>
</template>
<style>
	page{
		background: #fff;
	}
	.boxcou{
		width: 580rpx;
		height: 200rpx;
		position: absolute;
		left: 0;
		top: 0;
	}
	.tcoupon{
		width: 400rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 20rpx;
		margin-top: 24rpx;
		padding: 0 50rpx;
		color: #F77800;
	}
	.tcou{
		width: 500rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 46rpx;
		margin-top: 50rpx;
		color: #F77800;
	}
	.bgopc{
		opacity: .58;
	}
	.coubig{
		width: 580rpx;
		height: 200rpx;
	}
	.coubigs{
		width: 580rpx;
		height: 200rpx;
		opacity: 0.58;
	}
	.couspon{
		width: 580rpx;
		display: flex;
		height: 200rpx;
		position: relative;
		margin-bottom: 20rpx;
	}
	.cousponbox{
		width: 690rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		padding: 60rpx 30rpx;
	}
</style>
<script>
import { getCouponsUser } from "@/api/user";
import DataFormatT from "@/components/DataFormatT";

const NAME = "UserCoupon";

export default {
  name: "UserCoupon",
  components: {
    DataFormatT
  },
  props: {},
  data: function() {
    return {
      couponsList: [],
      loading: false
    };
  },
  watch: {
    $yroute: function(n) {
      var that = this;
      if (n.name === NAME) {
        that.getUseCoupons();
      }
    }
  },
  mounted: function() {
    this.getUseCoupons();
  },
  methods: {
    getUseCoupons: function() {
      let that = this,
        type = 3;
      getCouponsUser(type).then(res => {
        that.couponsList = res.data;
        that.loading = true;
      });
    }
  }
};
</script>

