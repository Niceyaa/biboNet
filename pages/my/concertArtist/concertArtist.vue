<template>
	<view v-if="loginStatus" class="concert-container">
		<back-header title="关注的艺人"></back-header>
		<view v-if="noArtist" class="concern-main">
			<view class="concern-item" v-for="item in artistList" :key="item.id">
				<view class="top">
					<image :src="item.img" @click="goToActor(item.id)"></image>
					<view class="content">
						<view class="left" @click="goToActor(item.id)">
							<view class="name">{{ item.name }}</view>
							<view class="desc">{{ item.desc }}</view>
						</view>
						<view class="right">
							<view @click="delConcern(item.id)" v-if="visible" class="concern-btn">已关注</view>
							<view v-else @click="addConcern(item.id)">未关注</view>
						</view>
					</view>
				</view>
				<view class="recent" @click="goToDetail(item.concert.id)">
					<view class="recent-top">
						<view>最近演出</view>
						<view>{{item.concert.start_at}}</view>
					</view>
					<view class="recent-main">
						<text>「{{item.concert.city_name.split("市")[0]}}站」</text>
						<text>{{item.concert.name}}</text>
						
					</view>
				</view>
			</view>
		</view>
		<view class="no-list" v-else>
			<view class="other-container">
				<image src="https://novelsys.oss-cn-shenzhen.aliyuncs.com/ticket/static/image/temp/noartist.png" mode=""></image>
				<view class="no-address">
					目前还没有关注的艺人
				</view>
			</view>
		</view>
	</view>
	<no-login v-else></no-login>
</template>

<script>
import noLogin from '../login/noLogin.vue'
import { formatDate } from "../../../common/formatDate.js"
import { operateActor } from "../../../Api/myApi/operateActor.js";
import { concernArtist } from "../../../Api/myApi/concernArtist.js";
import backHeader from '../../../components/childheader.vue';
export default {
	data() {
		return {
			artistList: [],
			visible: true,
			loginStatus: false,
			noArtist: true
		};
	},
	watch:{
		artistList(val){
			console.log(val)
		}
	},
	components: {
		backHeader,
		noLogin
	},
	methods: {
		delConcern(id){
				operateActor({
					opt: "delete",
					id: id
				}).then(res=>{
					uni.showToast({
						title:res.data.err_msg
					})
				})
			
		},
		addConcern(id){
			
				operateActor({
					opt: "add",
					id: id
				}).then(res=>{
					uni.showToast({
						title:res.data.err_msg
					})
				})
			
		},
		goToActor(id){
			uni.navigateTo({
				url:`/pages/index/homepage/homepage?id=${id}`
			})
		},
		goToDetail(id){
			console.log("去演出详情")
			uni.navigateTo({
				url:`/pages/index/yanchudetails/yanchudetails?id=${id}`
			})
		}
	},
	onShow() {
		concernArtist().then(res=>{
			this.artistList = res[1].data.data;
			if(this.artistList.length>0){
				this.noArtist = true;
			}else{
				this.noArtist = false;
			}
			console.log("关注艺人列表",res);
			this.artistList.forEach(item => {
					let t = new Date(item.concert.start_at);
					let y = t.getFullYear();
					let m = (t.getMonth().toString()).padStart(2,"0");
					let d = (t.getDate().toString()).padStart(2,"0");
					let h = t.getHours().toString().padStart(2,"0");
					let mi = t.getMinutes().toString().padStart(2,"0");
					item.concert.start_at = `${y}-${m}-${d}-${h}:${mi}`
					console.log(t)
					
					console.log(item.concert.start_at)
			})
			
		})
		/* this.artistList = [
				{
					id: 2,
					name: '狗脑壳',
					desc: '111',
					img: 'http://img0.imgtn.bdimg.com/it/u=4250058738,780121024&fm=11&gp=0.jpg',
					concert: {
						id: 8,
						start_at: 1591372800,
						name: 'Taylor Swift concert',
						city_name: '重庆市'
					}
				},
				{
					id: 1,
					name: '范冰冰',
					desc: '美女',
					img: 'https:\/\/ticket-app.oss-cn-shenzhen.aliyuncs.com\/files\/$RHJGM1N.jpg',
					concert: {
						id: 43,
						start_at: 1591372700,
						name: '那英演唱会',
						city_name: '天津市'
					}
				}
			]; */
	},
	onLoad() {
		if(uni.getStorageSync("loginStatus")){
			this.loginStatus = true;
		}
		console.log("loginStatus:",this.loginStatus)
	}
};
</script>

<style lang="less"> 
page{
	height: 100%;
}
	.concert-container{
		height: 100%;
		.concern-main{
			padding-top: 140rpx;
			height: 100upx;
			.concern-item{
				&:first-child{
					border-top: 1px solid #f0f0f0;
				}
				.top{
					padding: 41upx 40upx;
					display: flex;
					align-items: center;
					image{
						width: 100upx;
						height: 100upx;
						border-radius: 50upx;
						flex: 0 100upx;
					}
					.content{
						flex: 1;
						font-size: 24upx;
						color: #B2B2B2;
						margin-left: 21upx;
						display: flex;
						justify-content: space-between;
						align-items: center;
						.left{
							.name{
								font-size: 32upx;
								color: #000;
								font-weight: bold;
							}
							.desc{
								padding-top: 21upx;
							}	
						}
						.right{
							view{
								border-radius: 30upx;
								padding: 18upx 36upx;
								border: 1px solid #f0f0f0;
								&.concern-btn{
									background: #FF4657;
									color: #fff;
									border: none;
								}
							}
						}
					}
				}
				.recent{
					background: #F6F6F6;
					margin: 0 40upx;
					border-radius: 10upx;
					font-size: 24upx;
					padding: 0 20upx;
					.recent-top{
						padding: 30upx 0;
						display: flex;
						justify-content: space-between;
						color: #B2B2B2;
					}
					.recent-main{
						padding-bottom: 42upx;
						color: #222;
						font-family: PingFangSC-Medium;
						font-weight: bold;
						text{
							&:last-child{
								padding-left: 10upx;
							}
						}
					}
				}
			}
		}
		.no-list {
			height: 100%;
			background: #fff;
			display: flex;
			align-items: center;
			justify-content: center;
			.other-container {
				image{
					width: 393upx;
					height: 289upx;
				}
				.no-address{
					text-align: center;
					font-size: 26upx;
					color: #000000;
					font-family: PingFangSC-Regular;
				}
				
			}
		}
	}
</style>
