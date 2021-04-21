<template>
  <view class="evaluateWtapper">
    <view
      class="evaluateItem"
      v-for="(item, evaluateWtapperIndex) in reply"
      :key="evaluateWtapperIndex"
    >
      <view class="pic-text acea-row row-middle" style="justify-content: space-between;">
		  <view style="width: 400rpx; display: flex; align-items: center;">
			<view class="pictrue">
			  <image :src="item.avatar" class="image" />
			</view>
			<view class="acea-row row-middle">
			  <view class="name line1">{{ item.nickname==null?'匿名用户':item.nickname }}</view>
			  <view class="start" :class="'star' + item.star" v-if="item.star"></view>
			</view>
		</view>
		<view class="time">{{ item.createTime.substring(0,10) }}</view>
      </view>
      
      <view class="evaluate-infor">{{ item.comment }}</view>
      <view class="imgList acea-row">
        <view class="pictrue" v-for="(itemn, eq) in item.picturesArr" :key="eq">
          <image :src="itemn" class="image" />
        </view>
      </view>
      <view class="reply" v-if="item.merchantReplyContent">
        <span class="font-color-red">yshop店员</span>
        ：{{item.merchantReplyContent}}
      </view>
    </view>
  </view>
</template>
<script>
import { dataFormat } from "@/utils";

export default {
  name: "UserEvaluation",
  props: {
    reply: {
      type: Array,
      default: () => []
    }
  },
  data: function() {
    return {};
  },
  mounted: function() {},
  methods: {
    dataFormat
  }
};
</script>
