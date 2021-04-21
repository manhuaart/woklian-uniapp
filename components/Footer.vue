<template>
  <view>
    <view class="footer-bg"></view>
    <view id="footer" :class="[isIpx ? 'iphonex-footer' : '', 'acea-row row-middle'] ">
      <view
        class="item"
        :class="{ on: footerIndex == tabtarIndex }"
        v-for="(item, footerIndex) in footerList"
        :key="footerIndex"
      >
		<view class="iconfont">
		  <image :src="item.icon1" v-if='footerIndex != tabtarIndex' ></image>
		  <image :src="item.icon2" v-if='footerIndex == tabtarIndex' mode=""></image>
		</view>
        <view>{{ item.name }}</view>
      </view>
    </view>
  </view>
</template>
<script>
import { mapState, mapMutations, mapActions } from "vuex";
export default {
  name: "Footer",
  props: {},
  data: function() {
    return {
      footerList: [
        {
          name: "首页1",
          icon1: "../static/img/1.png",
          icon2: "../static/img/11.png",
          url: "/pages/home/index"
        },
        {
          name: "分类",
          icon1: "../static/img/2.png",
          icon2: "../static/img/22.png",
          url: "/pages/shop/GoodsClass/index"
        },
        {
          name: "购物车",
          icon1: "../static/img/4.png",
          icon2: "../static/img/44.png",
          url: "/pages/shop/ShoppingCart/index"
        },
        {
          name: "我的",
          icon1: "../static/img/5.png",
          icon2: "../static/img/55.png",
          url: "/pages/user/User/index"
        }
      ],
      isIpx: false
    };
  },
  computed: {
    ...mapState(["tabtarIndex"])
  },
  methods: {
  },
  mounted() {
    
  }
};
</script>
<style scoped>
  .iconfont image{
    width: 60rpx;
    height: 60rpx;
  }
</style>
