<template>
  <view ref="container" >
		<view class="pagesdwed">
			
		
		   <view class="sharebox">
			   <img src="https://attachments-manager.oss-cn-beijing.aliyuncs.com/kyd.png" class="imgpng" />
			   <view class="marquee">
				   <view class="marquee_box">
					   <view class="marquee_list" :class="{marquee_top:animate}">
						   <view v-for='(item,index) in txData' :key="index"><text>{{item.phone}}</text><text>刚刚提现{{item.price}}元</text></view>
					   </view>
				   </view>
			   </view>
		   </view>
		   <view class="btnShare">
			   <button open-type="share" class="btnBtn animats" v-if="showBtn==1">点击分享</button>
			   <button class="btnBtn animats" v-if='showBtn==2' @click="checkPhone">点击分享</button>
			   <button class="btnBtn animats" v-if="shenqing" @click="goToWord()">申请沃卡</button>
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
		</view>
  </view>
</template>
<script>
import { getUserInfo, getMenuUser, bindingPhone,bandPhones,getYzm } from "@/api/user";
import {
	mapState,
	mapMutations,
	mapActions
} from 'vuex';
export default {
  components: {
    
  },
  props: {},
	data: function() {
		return {
			txData: [
				{phone: "131****5628",price: '50'},
				{phone: "130****5683",price: '100'},
				{phone: "186****6375",price: '50'},
				{phone: "185****5269",price: '100'},
				{phone: '130****1273',price: '150'},
				{phone: '186****9156',price: '50'},
				{phone: '185****9023',price: '50'},
				{phone: '131****5628',price: '50'},
				{phone: '185****7960',price: '100'},
				{phone: '186****9156',price: '50'},
				{phone: '156****3592',price: '200'},
				{phone: '186****7150',price: '50'},
				{phone: '185****1683',price: '50'},
			], 
			animate: false,
			showBtn: 0,
			newPhone: '',
			newYzm: '',
			isdisable: false,
			verifyInfo: '获取验证码',
			codeInput: '',
			phMask: false,
			shenqing: false,
			shareUrl: '',
		}
	},
	mounted: function() {
		setInterval(this.showMarquee, 2000)
	},
	onReachBottom() {

	},
	computed:{
		...mapState({
		      // 箭头函数使代码更简练
		      userInfo: state => state.userInfo,
		 })
	},
	watch: {
	  userInfo(nextModel2) {
	    if(!nextModel2){
			uni.navigateTo({
				url: '/pages/authorization/index?shaUrl=shaUrl'
			})
		}
	  }
	},
	onShow(){
		if(!this.userInfo){
			uni.navigateTo({
				url: '/pages/authorization/index?shaUrl=shaUrl'
			})
		}else{
			if(this.userInfo.phone){
				this.getUrl(this.userInfo.phone)
			}
		}
		
	},
	onLoad: function(options) {
		var that=this
		if(options.showBtn){
			that.shenqing = true
		}else{
			that.shenqing = false
			if(that.userInfo.phone && that.userInfo.phone!='null'){
				//that.getUrl(that.userInfo.phone)
				that.showBtn = 1
			}else{
				that.showBtn = 2
			}
		}
	},
	onShareAppMessage: function(res) {
		var that=this
		if (res.from === 'button') { // 按钮上的分享
			console.log(res)
		}
		return {  //右上角分享
			title: '全民话费疯狂砍，砍至0元嗨到爆',
			path: '/pages/user/share/index?showBtn=showBtn',
			imageUrl: ''
		}
	},
	methods: {
		getUrl:function(phone){
			var that=this
			wx.request({
			  url: 'https://wlcx-provider.chinat5.com/api/card/generateSharingLinks?newPhoneNum='+phone, 
			  // url: 'http://192.168.3.38:8801/api/card/generateSharingLinks?newPhoneNum='+phone, 
			  header: {
			    'content-type': 'application/json' // 默认值
			  },
			  method: 'GET',
			  success (res) {
			    console.log(res.data)
				that.shareUrl = res.data.body.applyOrderUrl
			  }
			})
		},
		goToWord:function(url){
			var that=this
			let urls =  encodeURIComponent(that.shareUrl)
			uni.navigateTo({
				url: '/pages/webView/index?url='+urls
			})
		},
		checkPhone:function(){
			var that=this
			that.phMask = !that.phMask
		},
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
					  that.getUrl(that.newPhone)
					  that.showBtn = 1
				  }else{
					  uni.showToast({
						title: res.msg,
						icon: "none",
						duration: 2000
					  });
				  }
			  })
			  .catch(error => {
					uni.showToast({
						title: error.msg,
						icon: "none",
						duration: 2000
					});
					that.$store.dispatch("userInfo", true);
			  });
		},
		clearStorage(){
			uni.showToast({
				title: "清空成功",
				icon: "success",
				duration: 2000,
			})
		},
		showMarquee: function () {
			this.animate = true;
			setTimeout(()=>{
			this.txData.push(this.txData[0]);
			this.txData.shift();
			this.animate = false;
		},500)}
	}
}
</script>
<style scoped>
	.btnShare{
		width: 750rpx;
		height: 200rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 999999;
	}
	.btnBtn{
		padding: 0 100rpx;
		height: 80rpx;
		background: #fff;
		border-radius: 40rpx;
		text-align: center;
		line-height: 80rpx;
		color: #c04001;
		background: #fff3e5;
		box-shadow: 0 4px 6px rgba(0,0,0,.3);
		font-weight: bold;
	}
	.animats{
	    animation:mymove 3s infinite;
	    -webkit-animation:mymove 3s infinite; /*Safari and Chrome*/
	    animation-direction:alternate;/*轮流反向播放动画。*/
	    animation-timing-function: ease-in-out; /*动画的速度曲线*/
	    /* Safari 和 Chrome */
	    -webkit-animation:mymove 3s infinite;
	    -webkit-animation-direction:alternate;/*轮流反向播放动画。*/
	    -webkit-animation-timing-function: ease-in-out; /*动画的速度曲线*/
	}
	@keyframes mymove{
	    0%{
	    transform: scale(.9);  /*开始为原始大小*/
	    }
	    25%{
	        transform: scale(1); /*放大1.1倍*/
	    }
	    50%{
	        transform: scale(.9);
	    }
	    75%{
	        transform: scale(1);
	    }
	
	}
	.sharebox{
		width: 100%;
		position: relative;
	}
	.sharebox img{
		width: 100%;
		display: block;
	}
	.pagesdwed{
		background: linear-gradient(to right,#f67c01,#f46000);
		height: 100vh;
		position: relative;
	}
	.imgpng{
		width: 750rpx;
		height: 1334rpx;
	}
	.marquee_box {
		display: block;
		overflow: hidden;
		width: 400rpx;
		height: 60rpx;
		background: rgba(0,0,0,.2);
		position: absolute;
		left: 175rpx;
		top: 540rpx;
		z-index: 999;
		border-radius:6px;
	}
	
	.marquee_list {
		display: block;
		position: absolute;
		top: 0;
		left: 0;
	}
	.marquee_top {
		transition: all 0.5s;
		margin-top: -60rpx
	}
	.marquee_list view {
		height: 60rpx;
		line-height: 60rpx;
		font-size: 24rpx;
		color: #fff;
		padding: 0 20rpx;
		width: 360rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
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
</style>
