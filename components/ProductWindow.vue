<template>
  <view>
    <view class="product-window" :class="attr.cartAttr === true ? 'on' : ''">
      <view class="textpic acea-row row-between-wrapper">
        <view class="pictrue">
          <image :src="attr.productSelect.image" class="image" />
        </view>
        <view class="text">
          <view class="line1">{{ attr.productSelect.store_name }}</view>
          <view class="money font-color-red">
            ￥<text class="num">{{ attr.productSelect.price }}</text><text class="dsja">到手价</text><text class="yuanJa">原价{{attr.productSelect.otPrice}}</text>
          </view>
		  <text class="stocks">库存: {{ attr.productSelect.stock }}</text>
        </view>
        <view class="iconfont icon-guanbi" @click="closeAttr"></view>
      </view>
      <view class="productWinList">
        <view class="item" v-for="(item, indexw) in attr.productAttr" :key="indexw">
          <view class="title">{{ item.attrName }}</view>
          <view class="listn acea-row row-middle">
            <view
              class="itemn"
              :class="item.index == indexn ? 'on' : ''"
              v-for="(itemn, indexn) in item.attrValue"
              @click="tapAttr(indexw, indexn)"
              :key="indexn"
            >{{ itemn.attr }}</view>
          </view>
        </view>
      </view>
      <view class="cart cartNewCon">
        <view class="title">购买数量</view>
        <view class="carnum acea-row row-left">
          <view class="item reduce" :class="cartNum <= 1 ? 'on' : ''" @click="CartNumDes">-</view>
          <view class="item num">{{ cartNum }}</view>
          <view
            class="item plus"
            :class="
              cartNum >= attr.productSelect.stock
                ? 'on'
                : ''
            "
            @click="CartNumAdd"
          >+</view>
        </view>
      </view>
    </view>
    <view class="mask" @touchmove.prevent :hidden="attr.cartAttr === false" @click="closeAttr"></view>
  </view>
</template>
<script>
export default {
  name: "ProductWindow",
  props: {
    attr: {
      type: Object,
      default: () => {}
    },
    cartNum: {
      type: Number,
      default: () => 1
    }
  },
  data: function() {
    return {};
  },
  mounted: function() {
    console.log(this);
  },
  methods: {
    closeAttr: function() {
      this.$emit("changeFun", { action: "changeattr", value: false });
    },
    CartNumDes: function() {
      this.$emit("changeFun", { action: "ChangeCartNum", value: false });
    },
    CartNumAdd: function() {
      this.$emit("changeFun", { action: "ChangeCartNum", value: 1 });
    },
    tapAttr: function(indexw, indexn) {
      // 修改商品规格不生效的原因：
      // H5端下面写法，attr更新，但是除H5外其他端不支持，
      // 尽量避免下面的骚写法，不要在子组件内更新props
      // 这里修改是为了能获取到被选中的属性
      this.attr.productAttr[indexw].index = indexn;
      let that = this;
      let value = that
        .getCheckedValue()
        .sort()
        .join(",");
      that.$emit("changeFun", {
        action: "ChangeAttr",
        value: {
          value,
          indexw,
          indexn
        }
      });
    },
    //获取被选中属性；
    getCheckedValue: function() {
      let productAttr = this.attr.productAttr;
      let value = [];
      console.log(productAttr)
      for (let i = 0; i < productAttr.length; i++) {
        for (let j = 0; j < productAttr[i].attrValueArr.length; j++) {
          if (productAttr[i].index === j) {
            value.push(productAttr[i].attrValueArr[j]);
          }
        }
      }
      return value;
    }
  }
};
</script>
<style scoped>
	.product-window .textpic .text .money{
		margin-top: 20rpx!important;
	}
	.product-window{
		border-radius: 0!important;
	}
	.cartNewCon{
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding-bottom: 60rpx;
	}
	.stocks{
		font-size: 22rpx;
		color: #999999;
		margin-top: 20rpx;
	}
	.dsja{
		font-size: 22rpx;
		margin-left: 6rpx;
	}
	.yuanJa{
		color: #292929;
		font-size: 24rpx;
		margin-left: 20rpx;
	}
	.product-window .textpic .text .money .num{
		font-size: 46rpx!important;
	}
	.product-window .textpic .pictrue{
		width: 260rpx!important;
		height: 260rpx!important;
	}
	.product-window .textpic{
		padding: 0 30rpx!important;
	}
</style>
