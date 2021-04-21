<template>
  <view class="personal-data">
    <view class="wrapper">
      <view class="wrapList">
        <view class="item acea-row row-between-wrapper on">
          <view class="picTxt acea-row row-between-wrapper">
            <view class="pictrue" @tap="chooseImage">
              <div class="pictrue">
                <img :src="avatar" />
              </div>
              <image src="@/static/images/alter.png" class="alter" />
            </view>
            <view class="text">
              <view class="name line1">{{ userInfo.nickname }}</view>
              <view class="phone">
                绑定手机号：
                <text v-if="userInfo.phone">{{ userInfo.phone }}</text>
                <text v-else>未绑定</text>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>
    <view class="list">
      <view class="item acea-row ">
        <view class="lable">用户名：</view>
        <view class="input">
          <input type="text" v-model="realName" maxlength="20"  placeholder="请输入您的用户名"/>
        </view>
      </view>
      <view class="item acea-row ">
        <view class="lable">昵称：</view>
        <view class="input">
          <input type="text" v-model="userInfo.nickname" maxlength="20"   placeholder="请输入您的昵称"/>
        </view>
      </view>
      <view class="item acea-row">
        <view class="lable">性别：</view>
		<picker :range="sex" mode='selector' @change="sexChange" @cancel="cancelSelect">
			<view class="input" >{{  sex[sexIndex] }}</view>
			<!-- <view class="input">
			  <input type="text" v-model="userInfo.sss"  placeholder="请选择性别"/>
			</view> -->
		</picker>
      </view>	
      <view class="item acea-row">
        <view class="lable">出生年月：</view>
		 <picker mode="date" :value="userInfo.birthday | formatBirthday" :start="startDate" :end="endDate" @change="bindDateChange">
		      <view class="input"> 请选择 {{userInfo.birthday | formatBirthday}}</view>
		 </picker>
      </view>				
      <!-- <view class="item acea-row row-between-wrapper">
        <view>ID号</view>
        <view class="input acea-row row-between-wrapper">
          <input type="text" :value="userInfo.uid" disabled class="id" />
          <text class="iconfont icon-suozi"></text>
        </view>
      </view> -->
      <!-- <view class="item acea-row row-between-wrapper">
        <view>手机号：</view>
        <view class="input">
          <input type="text" v-if="userInfo.phone" v-model="userInfo.phone" />
          <input type="text" v-else value="未绑定" disabled class="id" />
        </view>
      </view> -->
      <!-- <view class="item acea-row row-between-wrapper" @click="goChangePassword()">
        <view>密码</view>
        <view class="input acea-row row-between-wrapper">
          <text>点击修改密码</text>
          <text class="iconfont icon-suozi"></text>
        </view>
      </view>-->
    </view>
    <view class="modifyBnt" @click="submit">提交申请</view>
    <view
      class="logOut cart-color acea-row row-center-wrapper"
      @click="logout"
      v-if="$deviceType=='app'"
    >退出登录</view>
	
  </view>
</template>
<script>
import { mapGetters } from "vuex";
import { trim, isWeixin, chooseImage } from "@/utils";
import { VUE_APP_API_URL } from "@/config";
import {
  postUserEdit,
  getLogout,
  switchH5Login,
  getUserInfo
} from "@/api/user";
import cookie from "@/utils/store/cookie";
import store from "@//store";
import dayjs from "dayjs";

export default {
  name: "PersonalData",
  components: {
	  // VueCoreImageUpload
  },
  data: function() {
	const currentDate = this.getDate({
	   format: true
	})  
    return {
	  sex:['请选择性别','男','女'],
	  sexIndex:0, //下标
      date: currentDate,  
	  //------------------------
      avatar: "",
      isWeixin: false,
      currentAccounts: 0,
      switchUserInfo: [],
      userIndex: 0,
	  realName: ''
    };
  },
    computed:{
    	startDate:function() {
    	    return this.getDate('start');
    	},
    	endDate:function(){
    	    return this.getDate('end');
    	},
    },
	computed: mapGetters(["userInfo"]),
  mounted: function() {
    this.avatar = this.userInfo.avatar;
	this.realName = this.userInfo.realName
    this.isWeixin = isWeixin();
    this.getUserInfo();
  },
  methods: {
//----------选择性别-----------
	// 点击完成按钮的时候触发，
	sexChange(e) {
		//通过e.detail.value获取值，获取的是自定义数据的下标
	    const val = e.detail.value
		console.log(val);
		this.sexIndex=val;
	},
	cancelSelect(e){},
//----------选择年月日-----------
        bindDateChange: function(e) {
            this.date = e.target.value
			// this.date = e.detail.value
			this.userInfo.birthday = e.detail.value
			console.log(this.date);
        },
	getDate(type) {
	    const date = new Date();
	    let year = date.getFullYear();
	    let month = date.getMonth() + 1;
	    let day = date.getDate();
	    if (type === 'start') { 
	          year = year - 60;
	    } else if (type === 'end') {
	             year = year + 2;
	    }
	    month = month > 9 ? month : '0' + month;;
	    day = day > 9 ? day : '0' + day;
	    return `${year}-${month}-${day}`;
	    },
//-----------------------------
    goChangePassword() {
      this.$yrouter.push("/pages/user/ChangePassword/index");
    },
    switchAccounts: function(index) {
      let that = this;
      this.userIndex = index;
      let userInfo = this.switchUserInfo[this.userIndex];
      if (this.switchUserInfo.length <= 1) return true;
      if (userInfo === undefined) {
        uni.showToast({
          title: "切换的账号不存在",
          icon: "none",
          duration: 2000
        });
        return;
      }
      if (userInfo.user_type === "h5") {
        switchH5Login()
          .then(({ data }) => {
            uni.hideLoading();
            const expires_time = dayjs(data.expires_time);
            store.commit("login", data.token, expires_time);
            that.$emit("changeswitch", false);
            location.reload();
          })
          .catch(err => {
            uni.hideLoading();
            uni.showToast({
              title:
                err.msg || err.response.data.msg || err.response.data.message,
              icon: "none",
              duration: 2000
            });
          });
      } else {
        cookie.set("loginType", "wechat", 60);
        uni.hideLoading();
        this.$store.commit("logout");
        this.$emit("changeswitch", false);
      }
    },
    getUserInfo: function() {
      let that = this;
      getUserInfo().then(res => {
		  console.log(res.data)
		  that.sexIndex = res.data.sex
		  // this.date = res.data.birthday
		  
        // let switchUserInfo = res.data.switchUserInfo;
        // for (let i = 0; i < switchUserInfo.length; i++) {
        //   if (switchUserInfo[i].uid == that.userInfo.uid) that.userIndex = i;
        //   if (
        //     !that.isWeixin &&
        //     switchUserInfo[i].user_type != "h5" &&
        //     switchUserInfo[i].phone === ""
        //   )
        //     switchUserInfo.splice(i, 1);
        // }
        // that.$set(this, "switchUserInfo", switchUserInfo);
      });
    },
    chooseImage() {
      chooseImage(img => {
        console.log(img)
        this.avatar = img;
      });
    },
    //提交申请按钮
    submit: function() {
      let userInfo = this.userInfo;
	  let dateStr =  this.date.replace(/\-/g, "");
	  console.log(dateStr)
	  postUserEdit({
        nickname: trim(this.userInfo.nickname), //昵称
		realName: trim(this.realName),  //用户名
		birthday:dateStr,  //出生年月
		sex:this.sexIndex, //性别		
        avatar: this.avatar //图片
      }).then(
        res => {
          this.$store.dispatch("userInfo", true);
           uni.showToast({
            title: res.msg,
            icon: "none",
            duration: 2000
          });
          this.$yrouter.back();
        },
        err => {
          uni.showToast({
            title:
              err.msg || err.response.data.msg || err.response.data.message,
            icon: "none",
            duration: 2000
          });
        }
      );
    },
    logout: function() {
      uni.showModal({
        title: "提示",
        content: "确认退出登录?",
        success: function(res) {
          if (res.confirm) {
            getLogout()
              .then(res => {
                this.$store.commit("logout");
                this.$yrouter.replace({
                  path: "/pages/user/Login/index",
                  query: {}
                });
              })
              .catch(err => {});
          } else if (res.cancel) {
            // console.log("用户点击取消");
          }
        }
      });
    },
	
  },
  filters: {
	  formatBirthday: function(date){	 
	  	//console.info(dayjs(birthday));// DateTimeFormat
		return dayjs(date.toString()).format('YYYY-MM-DD');
	  }
  }
};
</script>
<style scoped>
	.acea-row{
		align-items: center!important;
	}
</style>
