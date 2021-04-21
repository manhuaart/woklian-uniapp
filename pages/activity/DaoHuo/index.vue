<template>
  <view class="viewBOx">
	  <view class="viewpox">
		  <image src="../../../static/imgs/daohuo.png" class="viewBOx-image"></image>
		  <view>商品 在两个月内一旦有货，<br /> 可通过小程序服务通知接受到货提醒</view>
		  <view class="vbtn" @click="notice()">到货通知</view>
	  </view>
  </view>
</template>
<script>
import { getTx } from "@/api/activity";
export default {
  data: function() {
    return {
      
    }
  },
  mounted: function() {
    console.log(this.$yroute.query.proTitle)
	console.log(this.$yroute.query.id)
  },
  methods: {
	notice(){
		uni.requestSubscribeMessage({
			tmplIds: ["gx2ZZVSTDaXeW7OkeCXKVcXKS49Jm4Jnc8qG0sVA_BY"],//数组
			success:(res)=>{
				console.log(res);//值包括'accept':同意、'reject':拒绝、'ban':后台禁用
				if(res.errMsg==="requestSubscribeMessage:ok"){
					console.log('你成功了')
					this.getTixing()
				}
			},
			fail:(err)=>{
				
			}
		})
	},
	getTixing(){
		var that=this
		getTx({
			type: 1,
			productName: that.$yroute.query.proTitle,
			skId: that.$yroute.query.id
		}).then(res => {
			uni.showToast({
			  title: "设置提醒成功～",
			  icon: "none",
			  duration: 2000
			});
		})
		.catch(error => {
			console.log(error)
			uni.showToast({
			  title: "设置失败，请重试～",
			  icon: "none",
			  duration: 2000
			});
	    });
	}
  }
}
</script>
<style scoped lang="less">
	.viewBOx{
		width: 750rpx;
		height: 100vh;
		background: #fff;
	}
	.viewpox{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		text-align: center;
		font-size: 24rpx;
		padding-top: 40rpx;
	}
	.viewBOx-image{
		width: 216rpx;
		height: 218rpx;
		display: block;
		margin-bottom: 30rpx;
	}
	.vbtn{
		width: 394rpx;
		height: 64rpx;
		color: #fff;
		line-height: 64rpx;
		text-align: center;
		background: linear-gradient(90deg, #1571FC 0%, #11A0F0 100%);
		border-radius: 25px;
		font-size: 24rpx;
		margin-top: 80rpx;
	}
</style>
