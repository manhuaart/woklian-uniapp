<template>
	<view class="recommend" ref="container">
		<view class="title acea-row row-center-wrapper">
			<text class="iconfont icon-zhuangshixian"></text>
			<text class="name">为你推荐</text>
			<text class="iconfont icon-zhuangshixian lefticon"></text>
		</view>
		<view class="recommendList acea-row row-between-wrapper">
			<view
				@click="routerGo(item)"
				class="item"
				v-for="(item, recommendIndex) in hostProduct"
				:key="recommendIndex"
			>
				<view class="pictrue"><image :src="item.image" class="image" /></view>
				<view class="padbox">
					<view class="name line1">{{ item.storeName }}</view>
					<view style="margin-bottom: 10rpx;"><text class="ggbox">{{ item.keyword }}</text></view>
					<span class="linebox"><image src="../static/imgs/jin.png" class="imgw" ></image>可用补贴券{{ item.otPrice - item.price }}</span>
					<view class="money font-color-red">
						￥
						<text class="num">{{ item.price }}</text><text class="textFts">到手价</text><text class="textFt">原价{{item.otPrice}}</text>
					</view>
				</view>
					
			</view>
		</view>
		<Loading :loaded="loadend" :loading="loading"></Loading>
	</view>
</template>
<script>
import { getHostProducts } from '@/api/store';
import Loading from '@/components/Loading';
export default {
	name: 'Recommend',
	props: {},
	components: {
		Loading
	},
	data: function() {
		return {
			hostProduct: [],
			page: 1,
			limit: 20,
			loadTitle: '',
			loading: false,
			loadend: false
		};
	},
	mounted: function() {
		this.hostProducts();
	},
	methods: {
		routerGo(item) {
			this.$yrouter.push({ path: '/pages/shop/GoodsCon/index', query: { id: item.id } });
		},
		hostProducts: function() {
			let that = this;
			if (that.loading) return; //阻止下次请求（false可以进行请求）；
			if (that.loadend) return; //阻止结束当前请求（false可以进行请求）；
			that.loading = true;
			getHostProducts(that.page, that.limit).then(res => {
				that.loading = false;
				//apply();js将一个数组插入另一个数组;
				that.hostProduct.push.apply(that.hostProduct, res.data);
				that.loadend = res.data.length < that.limit; //判断所有数据是否加载完成；
				that.page = that.page + 1;
			});
		}
	},
	onReachBottom() {
		!this.loading && this.hostProducts();
	}
};
</script>
<style scoped>
	.textFt{
		text-decoration: line-through;
		margin-left: 10rpx;
	}
	.textFts{
		margin-left: 6rpx;
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
		top: -12rpx;
	  }
	.ggbox{
		border:1px solid rgba(239,158,26,1);
		padding: 2rpx 6rpx;
		font-size: 20rpx;
		display: inline-block;
		border-radius: 4rpx;
	}
	
	.recommend{
		background: #f2f2f2!important;
	}
	.recommend .recommendList{
		padding: 0 20rpx!important;
	}
	.recommend .recommendList .item{
		background: #fff;
		border-radius: 12rpx;
		width: 345rpx!important;
	}
	
	.padbox{
		padding: 20rpx;
	}
	.recommend .recommendList .item .money{
		margin-top: 10rpx!important;
	}
</style>
