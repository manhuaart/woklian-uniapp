<template>
  <view class="flash-sale" ref="container">
	<view class="scrollbox">
		<scroll-view scroll-y="false" scroll-x="true">
		  <view class="timeScroll">
			<view v-for="(item, index) in timeList" :key="index">
			  <view v-if="active==index" class="timeItem active" @click="setTime(index, item.time,item.endTime)">
				<view class="time">{{ item.time }}</view>
				<span class="state">{{ item.state }}</span>
			  </view>
			  <view v-if="active!=index" class="timeItem" @click="setTime(index, item.time,item.endTime)">
				<view class="time">{{ item.time }}</view>
				<view class="state">{{ item.state }}</view>
			  </view>
			</view>
		  </view>
		</scroll-view>
	</view>
	<view class="liststop">
		<view v-for="(item, index) in timeList" :key="index">
		  <view v-if="active == index">
			<!-- <view class="countDown font-color-red acea-row row-center-wrapper">
			  <view v-if="item.status === 0" class="activity">活动已结束</view>
			  <count-down
				:isDay="false"
				:tipText="'距结束仅剩 '"
				:dayText="false"
				:hourText="' : '"
				:minuteText="' : '"
				:secondText="false"
				:datatime="datatime"
				v-if="item.status === 1"
			  ></count-down>
			  <view v-if="item.status === 2" class="activity">活动即将开始</view>
			</view> -->
			<view class="list">
			  <view
				class="item acea-row row-between-wrapper"
				v-for="(itemSeckill, indexSeckill) in seckillList"
				:key="indexSeckill"
				 @click="goDetail(itemSeckill.id)"
			  >
				<view class="pictrue">
				  <image :src="itemSeckill.image" />
				  <view class="shouqing"  v-if="itemSeckill.stock ==itemSeckill.sales" >售罄</view>
				  <!-- <view class="shouqing" @click="goDetail(itemSeckill.id)" v-if="itemSeckill.stock ==itemSeckill.sales" >售罄</view> -->
				</view>
				<view class="text">
				  <view class="line1s">{{itemSeckill.title.length>35?itemSeckill.title.substring(0,35) + '...':itemSeckill.title}}</view>
				  <span class="btjin">可用补贴券<text class='col-cl'>{{itemSeckill.otPrice - itemSeckill.price}}</text></span>
				  <!-- <view class="proDs" v-if="item.status != 2">
					  <view class="bgfont" v-if="itemSeckill.stock >itemSeckill.sales">已抢{{itemSeckill.sales}}件</view>
					  <view class="bgfont" v-if="itemSeckill.stock == itemSeckill.sales">{{itemSeckill.sales}}件抢光了</view>
					  <view class="jindu" v-if="Math.round(itemSeckill.sales/itemSeckill.stock*100)>5" :style="{width: Math.round(itemSeckill.sales/itemSeckill.stock*100) + '%'}">进度条</view>
					  <view class="botbg"></view>
					  <text>{{Math.floor(itemSeckill.sales/itemSeckill.stock *100)}}%</text>
				  </view> -->
				  <view class="money">
					<text class="textFu">￥</text>
					<text class="num font-color-red" v-text="itemSeckill.price"></text>
					<!-- <text class="textsmalls">到手价</text> -->
					<text class="textsmall">￥{{itemSeckill.otPrice}}</text>
				  </view>
				</view>
				<view
				  class="grab bg-color-red"
				  v-if="item.status === 1 && itemSeckill.stock > itemSeckill.sales"
				  @tap.stop="goDetail(itemSeckill.id)"
				>立即抢购</view>
				<view class="grab bg-color-red opci" v-if="item.status === 1 && itemSeckill.stock ==itemSeckill.sales">已抢完</view>
				<view class="grab bg-color-reds" v-if="item.status === 2&&itemSeckill.isNotice === 0" @tap.stop="notice(itemSeckill.title,itemSeckill.id)">提醒我</view>
				<view class="grab bg-color-redsd" v-if="item.status === 2&&itemSeckill.isNotice === 1" >已预约</view>
				<view class="grab bg-color-red opci" v-if="item.status === 0">已结束</view>
			  </view>
			</view>
			<view
			  class="noCommodity"
			  style="background-color: #fff; padding:75rpx 0;"
			  v-if="seckillList.length === 0 && page > 1"
			>
			  <view class="noPictrue">
				<image src="@/static/images/noGood.png" class="image" />
			  </view>
			</view>
		  </view>
		</view>
	</view>
  </view>
</template>
<script>
import { getSeckillConfig, getSeckillList,getTx } from "@/api/activity";
import CountDown from "@/components/CountDown";
// import { Tab, Tabs } from "vant-weapp";
import Loading from "@/components/Loading";

export default {
  name: "GoodsSeckill",
  components: {
    CountDown
  },
  props: {},
  data: function() {
    return {
      headerImg: "",
      timeList: [],
	  sTime: '',
	  eTime: '',
      sticky: false,
      loading: false,
	  msTatus: 0,
      datatime: 0,
      active: 0,
      seckillList: [],
      status: false, //砍价列表是否获取完成 false 未完成 true 完成
      loadingList: false, //当前接口是否请求完成 false 完成 true 未完成
      page: 1, //页码
      limit: 5, //数量
      title: [],
	  currDate: ''
    };
  },
  mounted: function() {
	var that=this
	var date = new Date;
	var year = date.getFullYear();
	var month = date.getMonth() + 1;
	var day = date.getDate();
	month = (month < 10 ? "0" + month : month);
	day = (day < 10 ? "0" + day : day);
	that.currDate = year.toString() + '-' + month.toString() + '-' + day.toString()
    that.mountedStart();
  },
  onReachBottom() {
    !this.loadingList && this.getSeckillList();
  },
  methods: {
	getInfoSet(){
		wx.getSetting({
		  success (res) {
		    console.log(res.authSetting)
		    // res.authSetting = {
		    //   "scope.userInfo": true,
		    //   "scope.userLocation": true
		    // }
		  }
		})
	},
	notice(n,id){
		uni.requestSubscribeMessage({
			tmplIds: ["W8UR4WzPjRS-8aw5GIKgvW5rPU3_iki5C7J_W1gJYLk"],//数组
			success:(res)=>{
				console.log(res);//值包括'accept':同意、'reject':拒绝、'ban':后台禁用
				if(res.errMsg==="requestSubscribeMessage:ok"){
					console.log('你成功了')
					this.getTixing(n,id)
				}
			},
			fail:(err)=>{
				
			}
		})
	},
	getTixing(n,id){
		var that=this
		var starTime = that.currDate +' '+ that.sTime+':00'
		var starTimes = starTime.replace(/-/, '/')
		var timedates = new Date(starTimes).getTime()
		
		var endTime = that.currDate +' '+ that.eTime+':00'
		var endTimes = endTime.replace(/-/, '/')
		var timedated = new Date(endTimes).getTime()
		console.log(timedated)
		// var d=new Date(that.eTime);   //创建一个指定的日期对象
		// console.log(d);
		// console.log(that.formatDate(d));
		getTx({
			skStartDate: timedates,
			skEndDate: timedated,
			productName: n,
			skId: id
		}).then(res => {
			that.loadingList = false;
			that.status = false;
			that.page = 1;
			that.active = that.active;
			that.datatime = that.timeList[that.active].stop;
			that.seckillList = [];
			that.getSeckillList();
		});
	},
	formatDate(now) { 
		var year=now.getFullYear();  //取得4位数的年份
		var month=now.getMonth()+1;  //取得日期中的月份，其中0表示1月，11表示12月
		var date=now.getDate();      //返回日期月份中的天数（1到31）
		var hour=now.getHours();     //返回日期中的小时数（0到23）
		var minute=now.getMinutes(); //返回日期中的分钟数（0到59）
		var second=now.getSeconds(); //返回日期中的秒数（0到59）
		return year+"-"+month+"-"+date+" "+hour+":"+minute+":"+second; 
	},
    changeTime: function(index) {
      this.active = index;
      this.getSeckillList();
    },
    mountedStart: function() {
      var that = this;
      uni.showLoading();
      getSeckillConfig().then(res => {
        // that.$set(that, "headerImg", res.data.lovely);
        that.$set(that, "timeList", res.data.seckillTime);
        that.$set(that, "active", res.data.seckillTimeIndex);

        let title = [];
        title = res.data.seckillTime.map((item, index) => {
          return {
            name: "div",
            attrs: {
              class: "timeItem"
            },
            children: [
              {
                name: "div",
                attrs: {
                  class: "time"
                },
                children: [
                  {
                    type: "text",
                    text: item.time
                  }
                ]
              },
              {
                name: "div",
                attrs: {
                  class: "state"
                },
                children: [
                  {
                    type: "text",
                    text: item.state
                  }
                ]
              }
            ]
          };
        });
        that.$set(that, "title", title);
        that.datatime = that.timeList[that.active].stop;
		that.msTatus = that.timeList[that.active].status;
		console.log(that.msTatus)
        that.getSeckillList();
        that.$nextTick(function() {
          that.sticky = true;
          uni.hideLoading();
        });
      });
    },
    setTime: function(index,t,p) {
      var that = this;
      that.page = 1;
      that.loadingList = false;
      that.status = false;
      that.active = index;
      that.datatime = that.timeList[that.active].stop;
	  that.msTatus = that.timeList[that.active].status;
	  console.log(that.msTatus)
      this.seckillList = [];
	  that.sTime = t
	  that.eTime = p
	  console.log(p)
      that.getSeckillList();
    },
    getSeckillList: function() {
      var that = this;
      if (that.loadingList) return;
      if (that.status) return;
      var time = that.timeList[that.active].id;
      getSeckillList(time, {
        page: that.page,
        limit: that.limit
      }).then(res => {
        that.status = res.data.length < that.limit;
        that.seckillList.push.apply(that.seckillList, res.data);
        that.page++;
        uni.hideLoading();
      });
    },
    goDetail: function(id) {
      var that = this;
      var time = that.timeList[that.active].stop;
      this.$yrouter.push({
        path: "/pages/activity/SeckillDetails/index",
        query: {
          id,
          time,
		  msTatus: that.msTatus
        }
      });
    }
  }
};
</script>
<style scoped lang="less">
	::-webkit-scrollbar{
		width: 0;
		height: 0;
		color: transparent;
	}
	.shouqing{
		width: 144rpx;
		height: 144rpx;
		background: rgba(0,0,0,.5);
		display: flex;
		align-items: center;
		justify-content: center;
		position: absolute;
		left: 50%;
		top: 50%;
		margin-top: -72rpx;
		margin-left: -72rpx;
		border-radius: 50%;
		font-size: 44rpx;
		color: #fff;
	}
	.opci{
		opacity: 0.3465;
	}
	.bgfont{
		color: #fff;
		font-size: 20rpx;
		position: absolute;
		left: 10rpx;
		top: 0;
		z-index: 99;
		line-height: 30rpx;
	}
	.jindu{
		height: 30rpx;
		background: linear-gradient(90deg, #FC5016 0%, #FF2E43 100%);
		border-radius: 10px;
		position: absolute;
		left: 0;
		top: 0;
		font-size: 0;
		z-index: 80;
	}
	.botbg{
		width: 260rpx;
		height: 30rpx;
		background: linear-gradient(90deg, #FC5016 0%, #FF2E43 100%);
		opacity: 0.345;
		border-radius: 10px;
	}
	.proDs{
		width: 260rpx;
		height: 30rpx;
		border-radius: 10px;
		margin-top: 20rpx;
		position: relative;
		z-index: 10;
		line-height: 30rpx;
	}
	.proDs text{
		position: absolute;
		right: 10rpx;
		top: 0;
		font-size: 20rpx;
		z-index: 90;
		color: #fff;
		line-height: 30rpx;
	}
	.flash-sale .list .item .text .money{
		margin-top: 20rpx!important;
		position: absolute;
		left: 0;
		bottom: 10rpx;
	}
	.flash-sale .list .item .text .money .num{
		font-size: 34rpx!important;
	}
	.textFu{
		font-size: 26rpx;
		color: #D30202;
	}
	.textsmall{
		font-size: 18rpx;
		color: #BEBEBE;
		text-decoration: line-through;
		margin-left: 10rpx;
	}
	.textsmalls{
		font-size: 18rpx;
		color: #D30202;
		margin: 0 10rpx;
	}
	.line1s{
		max-height: 68rpx;
		line-height: 34rpx;
		font-size: 24rpx;
		margin-bottom: 14rpx;
		overflow: hidden;
		color: #292929;
	}
	.col-cl{
		color: #DA0000;
		margin-left: 6rpx;
	}
	.btjin{
		background: #FFE6DD;
		border-radius: 9px;
		padding: 4rpx 20rpx;
		font-size: 22rpx;
		color: #292929;
	}
	.cart-color{
		margin-top: 20rpx;
	}
	.bg-color-red{
		background: linear-gradient(90deg, #FC5016 0%, #FF2E43 100%)!important;
		border-radius: 5px!important;
		font-size: 24rpx!important;
		width: 148rpx!important;
		height: 68rpx!important;
		box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);
	}
	.bg-color-reds{
		background: linear-gradient(90deg, #1671FC 0%, #11A0F0 100%);
		border-radius: 5px!important;
		font-size: 24rpx!important;
		width: 148rpx!important;
		height: 68rpx!important;
		box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);
	}
	.bg-color-redsd{
		font-size: 24rpx!important;
		color: #11A0F0!important;
		width: 144rpx!important;
		height: 64rpx!important;
		line-height: 64rpx!important;
		box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);
		border-radius: 8px!important;
		position: relative;
	}
	.bg-color-redsd::after{
		position: absolute;
		content: '';
		border:1px solid #11A0F0;
		border-radius: 24rpx!important;
		width: 200%;
		height: 200%;
		top: 0;
		left: 0;
		transform: scale(0.5);
		transform-origin: 0 0;
	}
	.flash-sale .list .item .pictrue{
		width: 244rpx!important;
		height: 244rpx!important;
		position: relative;
	}
	.flash-sale .list .item .text{
		width: 380rpx!important;
		height: 244rpx!important;
		position: relative;
	}
	.flash-sale .list .item{
		height: 282rpx!important;
		padding: 0 20rpx!important;
		border-radius: 10px;
		background-color: #fff;
		border-bottom: none!important;
		margin-bottom: 20rpx;
		box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);
	}
	.scrollbox{
		width: 750rpx;
		height: 288rpx;
		background: linear-gradient(90deg, #FF430C 0%, #D30202 100%);
		border-radius: 0 0 10% 10%;
		padding-top: 20rpx;
	}
	.timeScroll {
	  display: flex;
	  align-items: center;
	  flex-direction: row;
	}

	.timeItem {
	  font-size: 0.22 * 100rpx;
	  color: #282828;
	  width: 150rpx;
	  text-align: center;
	  padding: 0.11 * 100rpx 0;
	  background-color: none;

	  &.active {
		.time {
		  color: #fff;
		  font-size: 40rpx;
		  opacity: 1;
		  height: 56rpx;
		  line-height: 0.56 * 100rpx;
		  margin-bottom: 20rpx;
		}

		.state {
		  background-color: #fff;
		  color: #E51C0B;
		  opacity: 1;
		  border-radius: 30rpx;
		  padding: 0 0.2 * 100rpx;
		  height: 0.34 * 100rpx;
		  line-height: 0.34 * 100rpx;
		  opacity: 1;
		  font-size: 24rpx;
		}
	  }
	}

	.timeItem .time {
	  font-size: 0.32 * 100rpx;
	  // font-weight: bold;
	  height: 0.32 * 100rpx;
	  line-height: 0.32 * 100rpx;
	  color: #fff;
	  opacity: .7;
	}

	.timeItem .state {
	  height: 0.32 * 100rpx;
	  line-height: 0.32 * 100rpx;
	  margin-top: 20rpx;
	  color: #fff;
	  opacity: .7;
	  font-size: 22rpx;
	}

	.activity {
	  color: #333;
	}
	.liststop{
		margin-top: -120rpx;
		width: 690rpx;
		padding: 0 30rpx;
	}
	.flash-sale .list .item .grab{
		right: 20rpx!important;
		bottom: 20rpx!important;
		line-height: 68rpx!important;
	}
</style>
