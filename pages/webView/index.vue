<template>
	<web-view :src="url" :progress="false"></web-view>
</template>
<script>
	export default {
		components: {
			
		},
		props: {},
		data: function() {
			return {
				url: '',
			};
		},
		computed:{
	
		},
		onShow: function() {
			
		},
		onLoad:function(option){
			console.log(option)
			var that=this
			that.url = decodeURIComponent(option.url)
		},
		methods: {
			
		}
	}

	
</script>
<style>
	
</style>
