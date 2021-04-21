<template>
  <view class="boxpox">
	 <view class="liat">
	   <image src="../../../static/imgs/piv.png" class="imgpiv"></image>
	   <view>文案品牌故事</view>
	   <text>快乐、轻松、时尚的厨房生活</text>
	 </view>
	 <view class="liat">
	   <image src="../../../static/imgs/piv.png" class="imgpiv"></image>
	   <view>文案品牌故事</view>
	   <text>快乐、轻松、时尚的厨房生活</text>
	 </view>
	 <view class="liat">
	   <image src="../../../static/imgs/piv.png" class="imgpiv"></image>
	   <view>文案品牌故事</view>
	   <text>快乐、轻松、时尚的厨房生活</text>
	 </view>       
 </view>
</template>
<script>
export default {
  data: function() {
    return {
	  
    };
  },
  mounted: function() {
	  
  },
  onReachBottom() {
    
  },
  methods: {
    
	
  }
};
</script>
<style scoped>
	.boxpox{
	    width: 750rpx;
	    padding: 30rpx 0;
	  }
	  .liat{
	    width: 690rpx;
	    padding: 0 30rpx;
	    display: flex;
	    flex-direction: column;
	    justify-content: center;
	    text-align: center;
	  }
	  .imgpiv{
	    width: 690rpx;
	    height: 290rpx;
	  }
	  .liat view{
	    color: #292929;
	    font-size: 32rpx;
	    padding: 20rpx 0;
	  }
	  .liat text{
	    color: #666;
	    font-size: 22rpx;
	    padding-bottom: 20rpx;
	  }
</style>
