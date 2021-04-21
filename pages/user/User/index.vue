<template>
  <view class="user">
    <view v-if="$store.getters.token||userInfo.uid">
      <view class="header bg-color-red acea-row row-between-wrapper">
        <image src="http://attachments-manager.oss-cn-beijing.aliyuncs.com/wobg.png" class="wobg" mode=""></image>
      </view>
      <view class="wrapper">
        <view class="nav acea-row row-middle">
			<!-- --------------------------- -->
			<view class="picTxt acea-row">
			  <view class="pictrues" @click="goPersonalData()">
				<image :src="userInfo.avatar" />
			  </view>
			  <view class="text">
				<view class="acea-row row-middle" @click="goPersonalData()">
					<view class="name line1">{{ userInfo.nickname }}</view>
				<!--<view class="member acea-row row-middle" v-if="userInfo.vip">
					 <image :src="userInfo.vipIcon" />
					<text>{{ userInfo.vipName }}</text>
				  </view> -->
				</view>
				<view @click="goPersonalData()" class="id" v-if="userInfo.phone">
					<text class="welcome">欢迎您，尊敬的会员</text>
				  <!-- <text>ID：{{ userInfo.uid || 0}}</text>
				  <text class="iconfont icon-bianji1"></text> -->
				</view>
				<!-- <button
				  open-type="getPhoneNumber"
				  @getphonenumber="getPhoneNumber"
				  class="binding"
				  v-else
				>
				  <text>绑定手机号</text>
				</button> -->
				<button
				  @click="getPhone()"
				  class="binding"
				  v-else
				>
				  <text>绑定手机号</text>
				</button>
			  </view>
			</view>
			<!-- <text class="iconfont icon-shezhi" @click="goPersonalData()"></text> -->
			<!-- --------------------------- -->
			<view @click="goUserCoupon()" class="subsidy">
			<!-- <view  class="subsidy"> -->
			    <text>我的补贴券：</text>
			    <text>{{ userInfo.newUserCouponAmount || 0 }}</text>
			  </view>
			</view>
<!--         <view @click="goUserAccount()" class="item">
            <text>我的余额</text>
            <text class="num">{{ userInfo.nowMoney || 0 }}</text>
          </view>
         <view @click="goIntegral()" class="item" v-else>
            <text>当前积分</text>
            <text class="num">{{ userInfo.integral || 0 }}</text>
          </view> -->
        <view class="myOrder">
          <view class="title acea-row row-between-wrapper">
            <text>我的订单</text>
            <text @click="goMyOrder(20)" class="allOrder">
              <text>查看全部订单</text>
              <text class="iconfont icon-jiantou"></text>
            </text>
          </view>
          <view class="orderState acea-row row-middle">
            <view @click="goMyOrder(0)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dfk.png" />
                <text
                  class="order-status-num"
                  v-if="userInfo.orderStatusNum.unpaidCount > 0"
                >{{ userInfo.orderStatusNum.unpaidCount }}</text>
              </view>
              <view>待付款</view>
            </view>
            <view @click="goMyOrder(1)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dfh.png" />
                <text
                  class="order-status-num"
                  v-if="userInfo.orderStatusNum.unshippedCount > 0"
                >{{ userInfo.orderStatusNum.unshippedCount }}</text>
              </view>
              <view>待发货</view>
            </view>
            <view @click="goMyOrder(2)" class="item">
              <view class="pictrue">
                <image src="@/static/images/dsh.png" />
                <text
                  class="order-status-num"
                  v-if="userInfo.orderStatusNum.receivedCount > 0"
                >{{ userInfo.orderStatusNum.receivedCount }}</text>
              </view>
              <text>待收货</text>
            </view>
            <view @click="goMyOrder(3)" class="item">
                 <view class="pictrue">
                   <image src="@/static/images/finish.jpg" />
                   <text
                     class="order-status-num"
                     v-if="userInfo.orderStatusNum.evaluatedCount+userInfo.orderStatusNum.completeCount > 0"
                   >{{ userInfo.orderStatusNum.evaluatedCount+userInfo.orderStatusNum.completeCount}}</text>
                 </view>
                 <text>已完成</text>
               </view>
            <view @click="goReturnList()" class="item">
              <view class="pictrue">
                <image src="@/static/images/sh.png" />
                <text
                  class="order-status-num"
                  v-if="userInfo.orderStatusNum.refundCount > 0"
                >{{ userInfo.orderStatusNum.refundCount }}</text>
              </view>
              <text>退/换货</text>
            </view>
          </view>
        </view>
        <view class="myService">
		   <view class="title acea-row row-middle">我的服务</view>
          <view class="serviceList acea-row row-middle">
			<view class="item">
			  <button open-type="contact" @click="handleContact" class="buttConBig">
			  	<view class="pictrue">
			  		<image src="../../../static/imgs/imgKf.png" mode="" ></image>
			  	</view>
			  	<view class="cell">联系客服</view>
			  </button>
			</view>
            <template v-for="(item, MyMenusIndex) in MyMenus">
              <view class="item" :key="MyMenusIndex" @click="goPages(MyMenusIndex)">
                <view class="pictrue">
                  <image :src="item.pic" />
                </view>
                <view class="cell">{{ item.name }}</view>
                <!-- <text class="iconfont icon-jiantou"></text> -->
              </view>
            </template>
            <!-- <view class="item" @click="goPages2()">
              <view class="pictrue"></view>
              <view class="cell">hexiao</view>
              <text class="iconfont icon-jiantou"></text>
            </view>-->
          </view>
		  <view class="outBtn" @click="goIndex">退出登录</view>
        </view>
      </view>
	  <!-- 绑定手机号 -->
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
	  <!-- 退出登录 -->
	  <view class="mask" v-if='mkfalse'>
	  	<view class="boxView">
	  		<view>您确定要退出登录吗？</view>
	  		<view>
	  			<text @click="cancel">取消</text>
	  			<text @click="clearStorage">确定</text>
	  		</view>
	  	</view>
	  </view>
	  <!-- 申请沃卡 -->
	  <view class="mask zIndex" v-if='mkWoka'>
	  	<view class="boxView">
	  		<view>云沃链卡未申请！去申请？</view>
	  		<view>
	  			<text @click="cancelWoka">取消</text>
	  			<text @click="goWoka">申请</text>
	  		</view>
	  	</view>
	  </view>
      <!-- <SwitchWindow
        v-on:changeswitch="changeswitch"
        :switchActive="switchActive"
        :login_type="userInfo.login_type"
      ></SwitchWindow>-->
    </view>
    <Authorization v-if="!$store.getters.token" />
  </view>
</template>
<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";
import { getUserInfo, getMenuUser, bindingPhone,bandPhones,getYzm } from "@/api/user";
import { isWeixin, VUE_APP_RESOURCES_URL } from "@/utils";
import SwitchWindow from "@/components/SwitchWindow";
import Authorization from "@/pages/authorization/index";

const NAME = "User";

export default {
  name: NAME,
  components: {
    SwitchWindow,
    Authorization
  },
  props: {},
  data: function() {
    return {
      MyMenus: [],
      switchActive: false,
      isWeixin: false,
	  newPhone: '',
	  newYzm: '',
	  isdisable: false,
	  verifyInfo: '获取验证码',
	  codeInput: '',
	  phMask: false,
	  mkfalse: false,
	  mkWoka: false,
    };
  },
  computed: mapGetters(["userInfo"]),
  methods: {
    ...mapMutations(["updateAuthorizationPage","logout"]),
	cancel(){
		this.mkfalse = !this.mkfalse;
	},
	clearStorage(){
		this.mkfalse = !this.mkfalse;
		uni.setStorageSync('login_status','')
		uni.setStorageSync('userInfo','')
		this.logout(false)
		uni.switchTab({url:'/pages/home/index'})
	},
	cancelWoka(){
		this.mkWoka = !this.mkWoka;
		this.phMask = !this.phMask;
	},
	goWoka(){
		this.mkWoka = !this.mkWoka;
		let urls =  encodeURIComponent('https://wx.chinat5.com/ziliaotianxie/index.html')
		//let urls =  encodeURIComponent('https://wx.chinat5.com/ziliaotianxie/successfulApplication.html')
		uni.navigateTo({
			url: '/pages/webView/index?url='+urls
		})
	},
	goIndex: function() {
	  this.mkfalse = !this.mkfalse;
	},
	handleContact (e) {
		console.log(e.detail.path)
		console.log(e.detail.query)
	},
	getPhone(){
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
					  console.log(res.status)
					  if(res.status==200){
						  uni.showToast({
						    title: "验证码发送成功",
						    icon: "none",
						    duration: 2000
						  });
					  }else if(res.status==404){
						  console.log('a')
						  that.phMask = !that.phMask;
						  that.mkWoka = !that.mkWoka
						  that.verifyInfo= '重新发送'
						  clearInterval(timer);
						  that.isdisable = false;
					  }else{
						  console.log('b')
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
						if(error.data.status==404){
							that.phMask = !that.phMask;
							that.mkWoka = !that.mkWoka;
						}else{
							uni.showToast({
							  title: error.msg,
							  icon: "none",
							  duration: 2000
							});
						}
						
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
    goReturnList() {
      this.$yrouter.push("/pages/order/ReturnList/index");
    },
    goMyOrder(type) {
      this.$yrouter.push({
        path: "/pages/order/MyOrder/index",
        query: {
          type
        }
      });
    },
    goUserCoupon() {
      this.$yrouter.push("/pages/user/coupon/UserCoupon/index");
    },
    goIntegral() {
      this.$yrouter.push("/pages/user/signIn/Integral/index");
    },
    goUserPromotion() {
      this.$yrouter.push("/pages/user/promotion/UserPromotion/index");
    },
    goUserAccount() {
      this.$yrouter.push({
        path: "/pages/user/UserAccount/index"
      });
    },
    goPersonalData() {
      this.$yrouter.push("/pages/user/PersonalData/index");
    },
    getPhoneNumber: function(e) {
      let thit = this;
      // 判断一下这里是不是小程序 如果是小程序，走获取微信手机号进行绑定
      if (e.mp.detail.errMsg == "getPhoneNumber:ok") {
        uni.showLoading({
          title: "绑定中"
        });
        // 获取当前环境的服务商
        uni.getProvider({
          service: "oauth",
          success: function(res) {
            // 此处可以排除h5
            if (res.provider) {
              uni.login({
                success: loginRes => {
                  bindingPhone({
                    code: loginRes.code,
                    encryptedData: e.mp.detail.encryptedData,
                    iv: e.mp.detail.iv
                  })
                    .then(res => {
                      console.log(res);
                      // this.User();
                      thit.$store.dispatch("userInfo", true);
                      uni.hideLoading();
                      uni.showToast({
                        title: res.msg,
                        icon: "success",
                        duration: 2000
                      });
                    })
                    .catch(error => {
                      uni.hideLoading();
                      thit.$store.dispatch("userInfo", true);
                      console.log(error);
                      uni.showToast({
                        title:
                          error.msg ||
                          error.response.data.msg ||
                          error.response.data.message,
                        icon: "none",
                        duration: 2000
                      });
                    });
                },
                fail() {
                  reject("绑定失败");
                }
              });
            }
          },
          fail() {
            reject("获取环境服务商失败");
          }
        });
      } else {
        uni.showToast({
          title: "已拒绝授权",
          icon: "none",
          duration: 2000
        });
      }
    },
    changeswitch: function(data) {
      this.switchActive = data;
    },
    User: function() {
      let that = this;
      getUserInfo().then(res => {
        that.user = res.data;
        that.orderStatusNum = res.data.orderStatusNum;
      });
    },
    MenuUser: function() {
      let that = this;
      getMenuUser()
        .then(res => {
          uni.hideLoading();
          that.MyMenus = res.data.routine_my_menus;
		  console.log(that.userInfo.phone)
		  if(that.userInfo.phone){
		  	that.phMask = false
		  }else{
		  	that.phMask = true
		  }  
        })
        .catch(error => {
          uni.hideLoading();
          console.log(error);
        });
    },
    goPages: function(index) {
      let url = this.MyMenus[index].uniapp_url;
      if (
        url === "/pages/user/promotion/UserPromotion/index" &&
        this.userInfo.statu === 1
      ) {
        if (!this.userInfo.isPromoter) {
          uni.showToast({
            title: "您还没有推广权限！！",
            icon: "none",
            duration: 2000
          });
          return;
        }
      }

      if (
        url === "/pages/orderAdmin/OrderIndex/index" &&
        !this.userInfo.adminid
      ) {
        uni.showToast({
          title: "您还不是管理员！！",
          icon: "none",
          duration: 2000
        });
        return;
      }
      console.log(this.userInfo)
      if (url === "/pages/orderAdmin/OrderCancellation/index" && !this.userInfo.checkStatus) {
        uni.showToast({
          title: "您没有核销权限,请后台店员设置！！",
          icon: "none",
          duration: 2000
        });
        return;
      }

      this.$yrouter.push({
        path: this.MyMenus[index].uniapp_url
      });
    },
    goPages2: function() {
      this.$yrouter.push({
        path: "/pages/orderAdmin/OrderCancellation/index"
      });
    }
  },
  watch: {
    userInfo() {
      this.MenuUser();
    }
  },
  onLoad(){
	  // if(this.userInfo.phone){
	  // 	this.phMask = !this.phMask
	  // }else{
	  // 	this.phMask = !this.phMask
	  // } 
  },
  onShow() {
    if (this.$store.getters.token) {
      //
      uni.showLoading({
        title: "加载中"
      });
      this.$store.dispatch("getUser", true);
      this.MenuUser();
      this.isWeixin = isWeixin();
    }
	
  },
  onHide() {
    console.log("离开用户中心");
    this.updateAuthorizationPage(false);
  }
};
</script>

<style lang="less">
page{
	background: #fff!important;
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
.zIndex{
	z-index: 9999999!important;
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
.wobg{
	width: 750rpx;
	height: 364rpx;
}
.user .wrapper .nav .subsidy{
	display: flex;
	align-items: center;
}
.user .wrapper .nav{
	box-shadow:15px 9px 21px 2px rgba(107,24,24,0.12)!important;
}
.user .wrapper{
	padding: 60rpx 0!important;
}
.user .wrapper .myOrder .orderState .item{
	font-size: 24rpx!important;
}
.user .wrapper .myService .serviceList .item {
	font-size: 24rpx!important;
}
.outBtn{
	width:80%;
	height:72rpx;
	box-shadow:0px 1px 5px 0px rgba(153,10,0,0.31);
	border-radius:18px;
	border:1px solid rgba(255,66,12,1);
	margin-left: 10%;
	line-height: 72rpx;
	text-align: center;
	color: #E40000;
	font-size: 28rpx;
	margin-top: 100rpx;
}
.user .wrapper .myService .serviceList .item{
	width: 20%!important;
}
.user{
	 background-color: #fff;
}
.footer-line-height {
  height: 1 * 100rpx;
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
.welcome{
	font-size: 26rpx!important;
	color: #666!important;
}
.user .wrapper .nav .picTxt .text .name{
	font-size: 42rpx!important;
	color: #292929!important;
}
.user .wrapper .nav .subsidy{
	margin-bottom: 40rpx!important;
}
.getYzm{
	position: absolute;
	right: 0;
	top: 20rpx;
	z-index: 99999;
	color: #D30202;
}
.peoConbox input.ipt{
	width: 80%;
	border-bottom:1px solid #f2f2f2;
	height: 60rpx;
}

.pictrues {
  position: relative;
  width: 136rpx!important;
  height: 136rpx!important;
  margin: 0 0 18rpx 40rpx;
}
.user .wrapper .nav .picTxt .text{
	width: 420rpx!important;
}
.user .wrapper .nav .subsidy{
	margin-left: 208rpx!important;
}
.pictrues image{
	width: 100%;
	height: 100%;
	border-radius: 50%;
	border: 3rpx solid #f5f5f5;

}
.switch-h5 {
  margin-left: 0.2 * 100rpx;
}

.binding {
  margin-top: 0.1 * 100rpx;
  display: inline-block;
  padding: 0.05 * 100rpx 0.2 * 100rpx;
  background-color: #ca1f10;
  border-radius: 50px;
  font-size: 0.22 * 100rpx;
  line-height: 1.5;
  border: 1px solid #e8695e;
  color: #ffffff;
}
.buttConBig{
	background: none;
	display: block;
	font-size: 24rpx;
	line-height: 1.5;
	padding: 0;
	color: #666;
	border: none!important;
}
.buttConBig text{
	display: block;
	margin-top: -4rpx;
}
.buttConBig::after {
  border: none;
  width: 60rpx!important;
}
.bimg{
	width: 32rpx;
	height: 32rpx;
	margin-top: 6rpx;
}
.user .header{
	height: 364rpx!important;
	background: none!important;
	border-radius: 0!important;
}
</style>
