<template>
  <view>
    <view class="searchGood">
      <view class="search acea-row row-between-wrapper">
        <view class="input acea-row row-between-wrapper">
          <text class="iconfont icon-sousuo2"></text>
          <!-- <form @submit.prevent="submit"></form> -->
          <input type="text" placeholder="点击搜索商品" v-model="search" />
        </view>
        <view class="bnt" @click="submit">搜索</view>
      </view>
	  <view  v-if="historyData.length">
	    <view class="title huobox">历史搜索<image src="../../../static/imgs/del.png" mode="" class="del" @click="deletes"></image></view>
	    <view class="list acea-row">
	      <view
	        class="bgItem"
	        v-for="items of historyData"
	        :key="items"
	        @click="toSearch(items)"
	      >{{ items }}</view>
	    </view>
	  </view>
	  <view class="mask" v-if='mkfalse'>
	  	<view class="boxView">
	  		<view>确认删除全部历史记录？</view>
			<view>
				<text @click="cancel">取消</text>
				<text @click="clearStorage">确定</text>
			</view>
	  	</view>
	  </view>
      <view v-if="keywords.length">
        <view class="title huobox">热门搜索<image src="../../../static/imgs/huo.png" mode="" class="huo"></image></view>
        <view class="list acea-row">
          <view
            class="item"
            v-for="keywordsKey of keywords"
            :key="keywordsKey"
            @click="toSearch(keywordsKey)"
          >{{ keywordsKey }}</view>
        </view>
      </view>
      <view class="line"></view>
      <!--      <GoodList></GoodList>-->
    </view>
    <!--<view class="noCommodity">-->
    <!--<view class="noPictrue">-->
    <!--<image src="@/static/images/noSearch.png" class="image" />-->
    <!--</view>-->
    <!--<recommend></recommend>-->
    <!--</view>-->
	
  </view>
</template>
<script>
// import GoodList from "@/components/GoodList";
import { getSearchKeyword } from "@/api/store";
import { trim } from "@/utils";
// import Recommend from "@/components/Recommend";
export default {
  name: "GoodSearch",
  components: {
    // Recommend,
    // GoodList
  },
  props: {},
  data: function() {
    return {
      keywords: [],
	  historyData: [],
      search: "",
	  mkfalse: false,
    };
  },
  mounted: function() {
	this.historyData = uni.getStorageSync('hisData').split(',');
	this.historyData.pop();
	this.historyData.reverse();
		
	if(this.historyData.length>10){
		this.historyData.pop();
	}
    this.getData();
  },
  methods: {
	deletes(){
		this.mkfalse = !this.mkfalse;
	},
	cancel(){
		this.mkfalse = !this.mkfalse;
	},
	clearStorage(){
		this.mkfalse = !this.mkfalse;
		this.historyData = [];
		uni.setStorageSync('hisData', '');
	},
    submit() {
      const search = trim(this.search) || "";
	  const arr = [];
      if (!search) return;
	  arr.push(search);
	  console.log(arr);
	  uni.setStorageSync('hisData', uni.getStorageSync('hisData').concat(arr+','));
	  console.log(uni.getStorageSync('hisData').concat(arr + ','));
      this.toSearch(search);
	  
    },
    toSearch(s) {
      this.$yrouter.push({ path: "/pages/shop/GoodsList/index", query: { s } });
    },
    getData() {
      getSearchKeyword().then(res => {
        this.keywords = res.data;
      });
    }
  }
};
</script>
<style >
page{
	background: #fff;
}
.mask{
	width: 750rpx;
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
.boxView{
	width: 500rpx;
	display: flex;
	flex-direction: column;
	justify-content: center;
	background: #fff;
	border-radius: 20rpx;
	padding: 40rpx 20rpx;
}
.boxView view{
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 32rpx;
}
.searchGood .search{
	background: #f2f2f2;
	padding: 20rpx 0 20rpx 30rpx!important;
	margin-top: 0!important;
}
.searchGood .search .input{
	background-color: #fff!important;
	border: 1px solid #D30202;
}
.boxView view text{
	width: 40%;
	height: 60rpx;
	text-align: center;
	background: #999;
	border-radius: 40rpx;
	background: #999;
	line-height: 60rpx;
	color: #fff;
	margin-top: 60rpx;
	font-size: 28rpx;
	margin-right: 5%;
}
.boxView view text:last-child{
	background: #DB2121;
	margin-right: 0;
}
.noCommodity {
  border-top: 0.05*100rpx solid #f5f5f5;
}
.del{
	width: 30rpx;
	height: 38rpx;
}
.searchGood .title{
	margin: 50rpx 30rpx 50rpx 30rpx!important;
}
.searchGood .list .item {
	border: none!important;
	border-radius: 0!important;
	padding: 0!important;
	height: auto!important;
	line-height: normal!important;
	width: 310rpx;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
	font-size: 22rpx;
	margin: 0 20rpx 20rpx 20rpx;

}

.searchGood .search .bnt{
	font-size: 28rpx!important;
	color: #DA3737!important;
}
.acea-row{
	/* justify-content: space-between; */
}
.bgItem{
	height: auto!important;
	line-height: normal!important;
	background: #f2f2f2;
	border: none!important;
	border-radius: 20px;
	padding: 4rpx 20rpx!important;
	font-size: 24rpx;
	margin: 0 0 20rpx 20rpx;
}
.huo{
	width: 30rpx;
	height: 34rpx;
}
.huobox{
	display: flex;
	align-items: center;
	justify-content: space-between;
	font-size: 32rpx!important;
	color: #292929!important;
}
</style>
