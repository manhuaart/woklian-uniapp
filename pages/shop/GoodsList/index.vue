<template>
  <view class="productList" ref="container">
    <form @submit.prevent="submitForm">
      <view class="search bg-color-red acea-row row-between-wrapper">
        <view class="input acea-row row-between-wrapper">
          <text class="iconfont icon-sousuo"></text>
          <input placeholder="搜索商品信息" v-model="where.keyword" disabled @click="goGoodSearch()" />
        </view>
        <view
          class="iconfont"
          :class="Switch === true ? 'icon-pailie' : 'icon-tupianpailie'"
          @click="switchTap"
        ></view>
      </view>
    </form>
    <view class="nav acea-row row-middle">
      <view class="item" :class="cur==false?'':'red'"  @click="set_where(0)">默认<text v-if="cur"></text></view>
      <view class="item" :class="cur1==false?'':'red'" @click="set_where(1)">
        价格
        <image src="@/static/images/horn.png" v-if="price === 0" />
        <image src="@/static/images/up.png" v-if="price === 1" />
        <image src="@/static/images/down.png" v-if="price === 2" />
		<text v-if="cur1"></text>
      </view>
      <view class="item" :class="cur2==false?'':'red'"  @click="set_where(2)">
        销量
        <image src="@/static/images/horn.png" v-if="stock === 0" />
        <image src="@/static/images/up.png" v-if="stock === 1" />
        <image src="@/static/images/down.png" v-if="stock === 2" />
		<text v-if="cur2"></text>
      </view>
      <!-- down -->
      <!-- <view class="item" :class="nows ? 'font-color-red' : ''" @click="set_where(3)">新品</view> -->
    </view>
    <view
      class="list acea-row row-between-wrapper"
      :class="Switch === true ? '' : 'on'"
      ref="container"
    >
      <view
        @click="goGoodsCon(item)"
        class="item"
        :class="Switch === true ? '' : 'on'"
        v-for="(item, productListIndex) in productList"
        :key="productListIndex"
        :title="item.storeName"
      >
        <view class="pictrue" :class="Switch === true ? '' : 'on'">
          <image :src="item.image" :class="Switch === true ? '' : 'on'" />
        </view>
        <view class="text" :class="Switch === true ? '' : 'on'">
          <view class="name line1">{{ item.storeName }}</view>
		  <view style="margin-bottom: 10rpx;"><text class="ggbox">{{ item.keyword }}</text></view>
		  <span class="linebox"><image src="../../../static/imgs/jin.png" class="imgw" ></image>可用补贴券{{ item.otPrice - item.price }}</span>
          <view class="money font-color-red" :class="Switch === true ? '' : 'on'">
            ￥
            <text class="num">{{ item.price }}</text><text class="txfont">到手价</text><text class="txfonts">原价￥{{ item.otPrice }}</text>
          </view>
          <!-- <view class="vip acea-row row-between-wrapper" :class="Switch === true ? '' : 'on'">
            <view class="vip-money">原价￥{{ item.otPrice }}</view>
            <view>已售{{ item.ficti }}件</view>
          </view> -->
        </view>
      </view>
    </view>
    <Loading :loaded="loadend" :loading="loading"></Loading>
    <view
      class="noCommodity"
      style="background-color: #fff;"
      v-if="productList.length === 0 && where.page > 1"
    >
      <view class="noPictrue">
        <image src="@/static/images/noGood.png" class="image" />
      </view>
    </view>
    <Recommend v-if="productList.length === 0 && where.page > 1"></Recommend>
  </view>
</template>
<script>
import Recommend from "@/components/Recommend";
import { getProducts } from "@/api/store";
import Loading from "@/components/Loading";

export default {
  name: "GoodsList",
  components: {
    Recommend,
    Loading
  },
  props: {},
  data: function() {
    // const { s = "", id = 0, title = "" } = this.$yroute.query;
    const s = "",
      id = 0,
      title = "";

    return {
      hostProduct: [],
      productList: [],
      Switch: true,
      where: {
        page: 1,
        limit: 8,
        keyword: s,
        sid: id, //二级分类id
        news: 0,
        priceOrder: "",
        salesOrder: ""
      },
      title: title && id ? title : "",
      loadTitle: "",
      loading: false,
      loadend: false,
      price: 0,
      stock: 0,
      nows: false,
	  cur: true,
	  cur1: false,
	  cur2: false,
    };
  },
  watch: {
    title() {
      this.updateTitle();
    },
    $yroute(to) {
      // if (to.name !== "GoodsList") return;
      // const { s = "", id = 0, title = "" } = to.query;
      // if (s !== this.where.keyword || id !== this.where.sid) {
      //   this.where.keyword = s;
      //   this.loadend = false;
      //   this.loading = false;
      //   this.where.page = 1;
      //   this.where.sid = id;
      //   this.title = title && id ? title : "";
      //   this.nows = false;
      //   this.$set(this, "productList", []);
      //   this.price = 0;
      //   this.stock = 0;
      //   this.get_product_list();
      // }
    }
  },
  mounted: function() {
    const { s = "", id = 0, title = "" } = this.$yroute.query;
    this.updateTitle();
    this.get_product_list();
  },
  onReachBottom() {
    !this.loading && this.get_product_list();
  },
  onHide() {
    // this.hostProduct = [];
    // this.productList = [];
    // this.Switch = true;
    // this.where = {
    //   page: 1,
    //   limit: 8,
    //   keyword: s,
    //   sid: id, //二级分类id
    //   news: 0,
    //   priceOrder: "",
    //   salesOrder: ""
    // };
    // this.loadTitle = "";
    // this.loading = false;
    // this.loadend = false;
    // this.price = 0;
    // this.stock = 0;
    // this.nows = fals;
  },
  methods: {
	goGoodSearch() {
		// this.$yrouter.push('/pages/shop/GoodsEvaluate/index');
		this.$yrouter.push('/pages/shop/GoodSearch/index');
	},
    goGoodsCon(item) {
      this.$yrouter.push({
        path: "/pages/shop/GoodsCon/index",
        query: { id: item.id }
      });
    },
    updateTitle() {
      // document.title = this.title || this.$yroute.meta.title;
    },
    get_product_list() {
      var that = this;
      this.setWhere();
      // if (to.name !== "GoodsList") return;
      const { s = "", id = 0, title = "" } = this.$yroute.query;
      if (s !== this.where.keyword || id !== this.where.sid) {
        this.where.keyword = s;
        this.loadend = false;
        this.loading = false;
        this.where.page = 1;
        this.where.sid = id;
        this.title = title && id ? title : "";
        this.nows = false;
        this.$set(this, "productList", []);
        this.price = 0;
        this.stock = 0;
        // this.get_product_list();
      }
      let q = that.where;
      getProducts(q).then(res => {
        that.loading = false;
        that.productList.push.apply(that.productList, res.data);
        that.loadend = res.data.length < that.where.limit; //判断所有数据是否加载完成；
        that.where.page = that.where.page + 1;
      });
    },
    submitForm: function() {
      this.$set(this, "productList", []);
      this.where.page = 1;
      this.loadend = false;
      this.loading = false;
      this.get_product_list();
    },
    //点击事件处理
    set_where: function(index) {
      let that = this;
      switch (index) {
        case 0:
		  that.cur = true;
		  that.cur1 = false;
		  that.cur2 = false;
		  that.where.page = 1;
          //return that.$yrouter.push({ path: "/pages/shop/GoodsClass/index" });
		  break;
        case 1:
          if (that.price === 0) that.price = 1;
          else if (that.price === 1) that.price = 2;
          else if (that.price === 2) that.price = 0;
          that.stock = 0;
		  that.cur = false;
		  that.cur1 = true;
		  that.cur2 = false;
          break;
        case 2:
          if (that.stock === 0) that.stock = 1;
          else if (that.stock === 1) that.stock = 2;
          else if (that.stock === 2) that.stock = 0;
          that.price = 0;
		  that.cur = false;
		  that.cur1 = false;
		  that.cur2 = true;
          break;
        // case 3:
        //   that.nows = !that.nows;
        //   break;
        default:
          break;
      }
      that.$set(that, "productList", []);
      that.where.page = 1;
      that.loadend = false;
      that.get_product_list();
    },
    //设置where条件
    setWhere: function() {
      let that = this;
      if (that.price === 0) {
        that.where.priceOrder = "";
      } else if (that.price === 1) {
        that.where.priceOrder = "asc";
      } else if (that.price === 2) {
        that.where.priceOrder = "desc";
      }
      if (that.stock === 0) {
        that.where.salesOrder = "";
      } else if (that.stock === 1) {
        that.where.salesOrder = "asc";
      } else if (that.stock === 2) {
        that.where.salesOrder = "desc";
      }
      that.where.news = that.nows ? "1" : "0";
    },
    switchTap: function() {
      let that = this;
      that.Switch = !that.Switch;
    }
  }
};
</script>
<style scoped lang="less">
.productList .list.on{
	border-top: none!important;
}
.red{
	color: #F11B09;
}
.productList .nav{
	border-bottom: 2px solid #e4e4e4;
}
.txfont{
	font-size: 20rpx;
	margin-left: 6rpx;
}
.txfonts{
	font-size: 20rpx;
	text-decoration: line-through;
	margin-left: 5px;
}
.noCommodity {
  border-top: 3px solid #f5f5f5;
  padding-bottom: 1px;
}
.ggbox{
	padding: 2rpx 6rpx;
	font-size: 20rpx;
	display: inline-block;
	position: relative;
}
.ggbox::after{
	position: absolute;
	content: '';
	border:1px solid rgba(239,158,26,1);
	border-radius: 14rpx;
	width: 200%;
	height: 200%;
	top: 0;
	left: 0;
	transform: scale(0.5);
	transform-origin: 0 0;
}
.bg-color-red{
	background: #f2f2f2!important;
}
.productList .search .input{
	border: 1px solid #D30202!important;
}
.productList .search{
	height: 100rpx!important;
}
.productList .search .icon-pailie, .productList .search .icon-tupianpailie{
	color: #999!important;
}
.productList .list{
	margin-top: 186rpx!important;
}
.productList .nav{
	top: 100rpx!important;
}
.linebox{
	background:linear-gradient(90deg,rgba(255,99,12,1) 0%,rgba(211,2,2,1) 100%);
	border-radius:7px;
	/* width: 270rpx; */
	font-size: 22rpx;
	color: #fff;
	/* display: flex; */
	margin-top: 10rpx;
	padding: 4rpx 16rpx 4rpx 46rpx;
	position: relative;
  }
  .imgw{
	width: 52rpx;
	height: 56rpx;
	position: absolute;
	left: 0;
	top: -16rpx;
  }
  .productList .list .item .text .money{
	  font-weight: normal!important;
  }
  .productList .list .item .text .money.on{
	  margin-top: 20rpx!important;
  }
  .productList .list{
	  padding: 0 20rpx 40rpx!important;
  }
	.productList .nav .item {
		position: relative;
		
	}
   .productList .nav .item text{
	   position: absolute;
	   left: 50%;
	   bottom: -28rpx;
	   background: #ff0000;
	   height: 2px; 
	   width: 50rpx;
	   display: block;
	   margin-left: -25rpx;
   }
   .productList .nav text:nth-child(2){
	   margin-left: -34rpx!important;
   }
   .productList .nav text:last-child{
   	   margin-left: -34rpx!important;
   }
</style>
