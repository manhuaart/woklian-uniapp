<template>
  <view class="group-con">
<!--    <view class="header acea-row row-between-wrapper">
      <view class="pictrue">
        <image :src="storeCombination.image" />
      </view>
      <view class="text">
        <view class="line1" v-text="storeCombination.title"></view>
        <view class="money">
          <text>￥</text>
          <text class="num" v-text="storeCombination.price"></text>
          <text class="team cart-color" v-text="storeCombination.people + '人拼'"></text>
        </view>
      </view>
      <view v-if="pinkBool === -1" class="iconfont icon-pintuanshibai"></view>
      <view v-else-if="pinkBool === 1" class="iconfont icon-pintuanchenggong font-color-red"></view>
    </view> -->
    <view class="wrapper">
      <view class="title acea-row row-center-wrapper">
        <view class="name">
		  <text>参与{{pinkT.nickname}}的拼单</text>
        </view>
      </view>
      <view class="tips-warp">
        <view class="tips" v-if="pinkBool === 1">恭喜您拼团成功</view>
        <view class="tips" v-else-if="pinkBool === -1">还差{{ count }}人，拼团失败</view>
        <view class="tips  acea-row row-center-wrapper" v-else-if="pinkBool === 0">
			 <text>仅剩{{  count }}个名额，</text>
			  <count-down
			    :isDay="false"
			    :tipText="false"
			    :dayText="false"
			    :hourText="' : '"
			    :minuteText="' : '"
			    :secondText="false"
			    :datatime="pinkT.stopTime/1000"
			  ></count-down>
			  <text>后结束</text>
	     </view>
      </view>
      <view
        class="list acea-row row-middle"
        :class="[pinkBool === 1 || pinkBool === -1 ? 'result' : '',iShidden ? 'on' : '']"
      >
        <view class="pictrue">
			<view class="rank">拼主</view>
            <image :src="pinkT.avatar" />
        </view>
        <view class="pictrue" v-for="(item, pinkAllIndex) in pinkAll" :key="pinkAllIndex"  v-if="pinkAll.length > 0">
            <image :src="item.avatar" />
        </view>
        <view class="pictrue" v-for="countIndex in count" :key="countIndex">
            <image class="img-none" src="@/static/images/vacancy.png" />
        </view>
      </view>
<!--      <view
        v-if="(pinkBool === 1 || pinkBool === -1) && count > 9"
        class="lookAll acea-row row-center-wrapper"
        @click="lookAll"
      >
        {{ iShidden ? "收起" : "查看全部" }}
        <text class="iconfont" :class="iShidden ? 'icon-xiangshang' : 'icon-xiangxia'"></text>
      </view> -->
      <view
        class="teamBnt"
        v-if="userBool === 1 && isOk == 0 && pinkBool === 0"
        @click="goPoster"
      >邀请好友参团</view>
      <view
        class="teamBnt"
        v-else-if="userBool === 0 && pinkBool === 0 && count > 0"
        @click="pay"
      >参与拼单</view>
      <view
        class="teamBnt"
        v-if="pinkBool === 1 || pinkBool === -1"
        @click="goDetail(storeCombination.id)"
      >再次开团</view>
      <view class="cancel" @click="getCombinationRemove" v-if="pinkBool === 0 && userBool === 1">
        <text class="iconfont icon-guanbi3"></text>
        <text>取消开团</text>
      </view>
      <view class="lookOrder" v-if="pinkBool === 1" @click="goOrder">
        <text>查看订单信息</text>
        <text class="iconfont icon-xiangyou"></text>
      </view>
    </view>
  </view>
</template>
<script>
import CountDown from "@/components/CountDown";
import { getCombinationPink, getCombinationRemove } from "@/api/activity";
import { postCartAdd } from "@/api/store";
import { isWeixin, parseQuery, handleQrCode } from "@/utils/index";

const NAME = "GroupRule";
export default {
  name: NAME,
  components: {
    CountDown
  },
  props: {},
  data: function() {
    return {
      currentPinkOrder: "", //当前拼团订单
      isOk: 0, //判断拼团是否完成
      pinkBool: 0, //判断拼团是否成功|0=失败,1=成功
      userBool: 0, //判断当前用户是否在团内|0=未在,1=在
      pinkAll: [], //团员
      pinkT: [], //团长信息
      storeCombination: [], //拼团产品
      pinkId: 0,
      count: 0, //拼团剩余人数
      iShidden: false,
    };
  },
  watch: {
    $yroute(n) {
      var that = this;
      if (n.name === NAME) {
        that.pinkId = that.$yroute.query.id;
        that.getCombinationPink();
      }
    }
  },
  mounted: function() {
    var that = this;
    let url = handleQrCode();
    if (url) {
      that.pinkId = url.pinkId;
    } else {
      that.pinkId = that.$yroute.query.id;
    }
    that.getCombinationPink();
  },
  methods: {
    pay: function() {
      var that = this;
      var data = {};
      data.productId = that.storeCombination.productId;
      data.cartNum = that.pinkT.totalNum;
      data.uniqueId = "";
      data.combinationId = that.storeCombination.id;
      data.new = 1;
      postCartAdd(data)
        .then(res => {
          that.$yrouter.push({
            path: "/pages/order/OrderSubmission/index",
            query: {
              id: res.data.cartId,
              pinkid: that.pinkId
            }
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
    },
    goPoster: function() {
      var that = this;
      this.$yrouter.push({
        path: "/pages/activity/Poster/index",
        query: {
          id: that.pinkId,
          type: 1
        }
      });
    },
    goOrder: function() {
      var that = this;
      this.$yrouter.push({
        path: "/pages/order/OrderDetails/index",
        query: {
          id: that.currentPinkOrder
        }
      });
    },
    //拼团列表
    goList: function() {
      this.$yrouter.push({
        path: "/pages/activity/GoodsGroup/index"
      });
    },
    //拼团详情
    goDetail: function(id) {
      this.$yrouter.push({
        path: "/pages/activity/GroupDetails/index",
        query: {
          id
        }
      });
    },
    //拼团信息
    getCombinationPink: function() {
      var that = this;
      getCombinationPink(that.pinkId).then(res => {
        that.$set(that, "storeCombination", res.data.storeCombination);
        that.$set(that, "pinkT", res.data.pinkT);
        that.$set(that, "pinkAll", res.data.pinkAll);
        that.$set(that, "count", res.data.count);
        that.$set(that, "userBool", res.data.userBool);
        that.$set(that, "pinkBool", res.data.pinkBool);
        that.$set(that, "isOk", res.data.isOk);
		that.$set(that, "isOk", res.data.isOk);
        that.$set(that, "currentPinkOrder", res.data.currentPinkOrder);
      });
    },
    //拼团取消
    getCombinationRemove: function() {
      var that = this;
      getCombinationRemove({
        id: that.pinkId,
		// id: that.pinkT.orderId,
        cid: that.storeCombination.id
      })
        .then(res => {
          uni.showToast({
            title: res.msg,
            icon: "none",
            duration: 2000
          });
        })
        .catch(res => {
          uni.showToast({
            title: res.msg,
            icon: "none",
            duration: 2000
          });
        });
    },
    lookAll: function() {
      this.iShidden = !this.iShidden;
    }
  }
};
</script>

<style lang="less">
.tips-warp {
  text-align: center;
  margin-top: 10rpx;
}
</style>
