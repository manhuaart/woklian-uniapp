<template>
  <view class="addAddress absolute">
    <view class="list">
      <view class="item acea-row row-between-wrapper">
        <view class="name">收货人</view>
        <input type="text" placeholder="请输入收货人姓名" v-model="userAddress.realName" required />
      </view>
      <view class="item acea-row row-between-wrapper">
        <view class="name">联系电话</view>
        <input type="text" placeholder="请输入联系电话" maxlength="11" v-model="userAddress.phone" required />
      </view>
      <view class="item acea-row row-between-wrapper">
        <view class="name">所在地区</view>
        <view class="picker acea-row row-between-wrapper select-value form-control">
          <view class="address">
            <CitySelect
              ref="cityselect"
              :defaultValue="addressText"
              @callback="result"
              :items="district"
            ></CitySelect>
          </view>
          <view class="iconfont icon-dizhi font-color-red"></view>
        </view>
      </view>
      <view class="item acea-row row-between-wrapper">
        <view class="name">详细地址</view>
        <input type="text" placeholder="请填写具体地址" v-model="userAddress.detail" required />
      </view>
    </view>
    <view class="default acea-row row-middle">
      <view class="select-btn">
        <view class="checkbox-wrapper">
			<span class="def">设置为默认地址</span>
		    <switch  @change="ChangeIsDefault"  value :checked="userAddress.isDefault ? true : false"/>
          <!-- <checkbox-group @change="ChangeIsDefault"> -->
            <!-- <label class="well-check"> -->
              <!-- <checkbox value :checked="userAddress.isDefault ? true : false"></checkbox> -->
              <!-- <text class="def">设置为默认地址</text> -->
            <!-- </label> -->
          <!-- </checkbox-group> -->
        </view>
      </view>
    </view>
    <view></view>
    <view class="keepBnt" @tap="submit">保存</view>
    <view class="wechatAddress" v-if="isWechat && !id" @click="getAddress">导入微信地址</view>
  </view>
</template>

<script type="text/babel">
import {
	mapState,
	mapMutations,
	mapActions
} from 'vuex';
import CitySelect from "@/components/CitySelect";
import { getAddress, postAddress, getCity,getCityIdStory  } from "@/api/user";
import attrs, { required, chs_phone } from "@/utils/validate";
import { validatorDefaultCatch } from "@/utils/dialog";
// import { openAddress } from "@/libs/wechat";
import { isWeixin } from "@/utils";
var QQMapWX = require('../../../../utils/qqmap-wx-jssdk.min.js');
var qqmapsdk;
export default {
  name: "AddAddress",
  components: {
    CitySelect
  },
  data() {
    return {
      district: [],
      id: 0,
      userAddress: { isDefault: 0 },
      address: {},
      isWechat: isWeixin(),
      addressText: ""
    };
  },
  onLoad:function(){
  	
  	qqmapsdk = new QQMapWX({
  	  key: '544BZ-J6U3Q-RUW5Q-G7LYU-D55WZ-D3BCO' //自己的key秘钥 
  	});
  	
  },
  computed:{
  	...mapState({
  	      // 箭头函数使代码更简练
  	      longitude: state => state.location.longitude,
  		  latitude: state => state.location.latitude
  	 })
  },
  mounted: function() {
    let id = this.$yroute.query.id;
    this.id = id;
	console.log(this.$yroute.query.url)
    this.getUserAddress();
    this.getCityList();
	if(this.$yroute.query.url){
		this.getLocal(this.latitude, this.longitude)
	}
  },
  watch: {
    addressText(nextModel2) {
      console.log(nextModel2, 8585858585);
    }
  },
  methods: {
	getLocal(latitude, longitude) {
		let vm = this;
		qqmapsdk.reverseGeocoder({
		  location: {
			latitude: latitude,
			longitude: longitude
		  },
		  success: function (res) {
			// console.log(JSON.stringify(res));
			console.log(res)
			let province = res.result.ad_info.province
			let city = res.result.ad_info.city
			let district = res.result.ad_info.district
			// vm.setData({
			//   province: province,
			//   city: city,
			//   latitude: latitude,
			//   longitude: longitude
			// })
			console.log(province)
			console.log(city)
			vm.getCityIds(province,city,district);
		  },
		  fail: function (res) {
			console.log(res);
		  },
		  complete: function (res) {
			// console.log(res);
		  }
		});
	},
	getCityIds:function(province,city,district){
		let that = this;
		getCityIdStory(province,city,district)
		  .then(res => {
		    that.addressText = province + city + district
			that.address.city = city
			that.address.city_id=res.data.city_id
			that.address.district = district
			that.address.province = province
		  })
		  .catch(err => {
		    that.addressText = '请选择'
		  });
	},
    getCityList: function() {
      let that = this;
      getCity()
        .then(res => {
          that.district = res.data;
          that.ready = true;
        })
        .catch(err => {
          that.$dialog.error(err.msg);
        });
    },
    getUserAddress: function() {
      if (!this.id) return false;
      let that = this;
      getAddress(that.id).then(res => {
        that.userAddress = res.data;
        that.addressText =
          res.data.province+ res.data.city + res.data.district;
        that.address.province = res.data.province;
        that.address.city = res.data.city;
        that.address.district = res.data.district;
      });
    },
    getAddress() {},
    async submit() {
      console.log(this);
      console.log(this.address);
      console.log(this.addressText);
      let name = this.userAddress.realName,
        phone = this.userAddress.phone,
        addressText = this.addressText,
        detail = this.userAddress.detail,
        isDefault = this.userAddress.isDefault;
      try {
        await this.$validator({
          name: [
            required(required.message("姓名")),
            attrs.range([2, 16], attrs.range.message("姓名"))
          ],
          phone: [
            required(required.message("联系电话")),
            chs_phone(chs_phone.message())
          ],
          addressText: [required("请选择地址")],
          detail: [required(required.message("具体地址"))]
        }).validate({ name, phone, addressText, detail });
      } catch (e) {
        return validatorDefaultCatch(e);
      }
      try {
        let that = this,
          data = {
            id: that.id,
            real_name: name,
            phone: phone,
            address: this.address,
            detail: detail,
            is_default: isDefault ? true : false,
            post_code: ""
          };
        postAddress(data).then(function() {
          if (that.id) {
            uni.showToast({
              title: "修改成功",
              icon: "none",
              duration: 2000
            });
          } else {
            uni.showToast({
              title: "已取消绑定",
              icon: "none",
              duration: 2000
            });
            // that.$yrouter.replace({
            //   path: "/pages/user/PersonalData/index"
            // });
          }
          that.$yrouter.go(-1);
        });
      } catch (err) {
        uni.showToast({
          title: err.msg || err.response.data.msg || err.response.data.message,
          icon: "none",
          duration: 2000
        });
      }
    },
    ChangeIsDefault: function() {
      this.userAddress.isDefault = !this.userAddress.isDefault;
    },
    result(values) {
      console.log(this);
      console.log(values);
      this.address = {
        province: values.province.name || "",
        city: values.city.name || "",
        district: values.district.name || "",
        city_id: values.city.id
      };
      this.addressText = `${this.address.province}${this.address.city}${this.address.district}`;
      // this.addressText =
      //   this.address.province + this.address.city + this.address.district;
    }
  }
};
</script>

<style lang="less">
.address {
  text {
    width: 100%;
    display: block;
  }
}
*{
   background-color: #fff;
}
  .addAddress{
     background-color: #fff;
  }
</style>
