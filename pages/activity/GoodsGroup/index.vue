<template>
  <view class="group-list" ref="container">
    <view class="list" v-if="combinationList.length>0">
      <view
        class="item acea-row "
        v-for="(item, combinationListIndex) in combinationList"
        :key="combinationListIndex"
        @click="link(item.id)"
      >
        <view class="pictrue">
          <image :src="item.image" />
        </view>
        <view class="text">
          <view class="line-title">{{item.title.length>35?item.title.substring(0,35) + '...':item.title}}</view>
          <view class="acea-row">
            <!-- <view class="team acea-row row-middle cart-color"> -->
				<span class="btjin">可用补贴券<text class='col-cl'>3366</text></span>
              <!-- <view class="iconfont icon-pintuan"></view>
              <view class="num" v-text="item.people + '人团'"></view> -->
            <!-- </view> -->
          </view>
          <view class="bottom acea-row row-between-wrapper">
			  <view class="bottom-di">
            <view class="money">
              ￥<text class="num">{{item.price}}</text>
              <!-- <text class="y-money" v-text="'￥' + item.productPrice"></text> -->
			  <text class="y-money">已拼<text class="tex">{{item.splledCount}}</text>件</text>
            </view>
           <view class="user-img">
                <view class="img" v-for="(items,avatarIndex) in  item.avatarList "  :key="avatarIndex"  v-if='avatarIndex<2'>
					<image :src="items" />
                </view>
            </view>
          </view>
		  </view>
        </view>
      </view>
      <Loading :loaded="status" :loading="loadingList"></Loading>
    </view>
    <view class="noCommodity" style="background-color: #fff;" v-if="combinationList.length === 0">
      <view class="noPictrue">
        <image src="@/static/images/noGood.png" class="image" />
      </view>
    </view>
  </view>
</template>
<script>
import { getCombinationList } from "@/api/activity";
import Loading from "@/components/Loading";

export default {
  name: "GoodsGroup",
  components: {
    Loading
  },
  props: {},
  data: function() {
    return {
      combinationList: [],
      status: false, //砍价列表是否获取完成 false 未完成 true 完成
      loading: false, //当前接口是否请求完成 false 完成 true 未完成
      page: 1, //页码
      limit: 20, //数量
      loadingList: false
    };
  },
  mounted: function() {
    // document.querySelector('body').setAttribute('style', 'background-color:#eb3729');
    this.getCombinationList();
  },
  onReachBottom() {
    !this.loadingList && this.getCombinationList();
  },
  methods: {
    getCombinationList: function() {
      var that = this;
      if (that.loading) return;
      if (that.status) return;
      getCombinationList({ page: that.page, limit: that.limit }).then(res => {
        that.status = res.data.length < that.limit;
        that.combinationList.push.apply(that.combinationList, res.data);
        that.page++;
        that.loading = false;
      });
    },
    link: function(id) {
      this.$yrouter.push({
        path: "/pages/activity/GroupDetails/index",
        query: { id }
      });
    }
  }
};
</script>
<style scoped lang="less">
	.group-list {
	  position: fixed;
	  background:#fff;
	  width: 100%;
	  height: 100%;
	  overflow: auto;
	}
	.line-title{
		max-height: 68rpx;
		line-height: 34rpx;
		font-size: 24rpx;
		margin-bottom: 14rpx;
		overflow: hidden;
		color: #292929;
	}
	
</style>