<template>
	<view class="content">
        <image class="logo" :src="imageSrc" :mode="mode"></image>
		<view>
            <text class="title">{{title}}</text>
        </view>
			<view >
			<text>{{faceValue}}</text>
		</view>

		<view>
		    <text class="title" >皮肤健康</text>
		</view>
		<view>
		    <text>{{health}}</text>
		</view>
		
		
		<view class="uni-padding-wrap uni-common-mt">
		    <button type="primary" @click="chooseImage">上传图片</button>
		</view>	
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageSrc:'',
				mode:'scaleToFill',
				title: 'AI综合评分',
				faceValue:83.331,
				health:30.706,
			}
		},
		onLoad() {
			this.imageSrc = '../../static/show.jpg'
			this.faceValue = 66
			this.health = 66
		},
		methods: {
			chooseImage: function() {
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: [],
					success: (res) => {
						var imageSrc = res.tempFilePaths[0]
						var api_key = 'MiewUBH3KPni_mgjLpDatN5_9i7Gab4C'
						var api_secret = 'oAXrUjAp6V3YqLy0aiceLsljGAwiMqWd'
						uni.uploadFile({
							url: 'https://api-cn.faceplusplus.com/facepp/v3/detect',
							filePath: imageSrc,
							name: 'image_file',
							formData:{
								'api_key':api_key,
								'api_secret':api_secret,
								'return_attributes':'beauty,skinstatus',
							},
							success: (res) => {
								let info = res.data
								let obj = JSON.parse(info)
								if (obj.faces[0] == null){
									this.faceValue = "未发现人脸，请上传脸部照片"
									this.health = "我可是世界级的人脸识别API,你别糊弄我！"
								}else{
									var female_score = (obj.faces[0].attributes.beauty.female_score)
									var male_score = (obj.faces[0].attributes.beauty.female_score)
									var health = obj.faces[0].attributes.skinstatus.health
									var stain = obj.faces[0].attributes.skinstatus.stain
									var acne  = obj.faces[0].attributes.skinstatus.acne
									var dark_circle = obj.faces[0].attributes.skinstatus.dark_circle
									console.log(health)
									uni.showToast({
										title: '上传成功',
										icon: 'success',
										duration: 1000
									})
									this.imageSrc = imageSrc
									this.faceValue = (female_score+male_score)/2
									this.health = health
								}
							},
							fail: (err) => {
								console.log('uploadImage fail', err);
								uni.showModal({
									content: err.errMsg,
									showCancel: false
								});
							}
						});
					},
					fail: (err) => {
						console.log('chooseImage fail', err)
					}
				})
			}
		}
	}
</script>

<style>
	.content {
		text-align: center;
	}
	.title {
		font-size: 36upx;
		color: #8f8f94;
	}
	
	.progress_box{
		position: relative;
		width:220upx;
		height: 220upx;  
		display: flex;  
		align-items: center;
		justify-content: center;
		background-color: #eee;
	}
	.progress_bg{
		position: absolute;
		width:220upx;
		height: 220upx; 
	}
	.progress_canvas{ 
		width:220upx;
		height: 220upx; 
	} 
	.progress_text{ 
		position: absolute; 
		display: flex;  
		align-items: center;
		justify-content: center
	}
	.progress_info{   
		font-size: 36rpx;
		padding-left: 16rpx;
		letter-spacing: 2rpx
	} 
	.progress_dot{
		width:16rpx;
		height: 16rpx;  
		border-radius: 50%;
		background-color: #fb9126;
	}
	.uni-common-mt{
		margin-top: 160upx;
	}
	

</style>
