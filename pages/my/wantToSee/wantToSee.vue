<template>
	<view v-if="loginStatus" class="want-see">
		<back-header title="想看的演出"></back-header>
		<view v-if="noConcert" class="see-main">
			<view @click="goToDetail(item.id)" class="concern-item" v-for="item in wannaSee" :key="item.id">
				<view class="concern-left">
					<image :src="item.poster" mode=""></image>
				</view>
				<view class="concern-right">
					<view class="right-title">【{{item.city_name}}站】《{{item.name}}》</view>
					<view class="right-time">{{item.start_at}}-{{item.end_at}}</view>
					<view class="right-time">{{item.venue.name}}</view>
					<view class="bottom-wraper">
						<view class="price">
							<text>{{item.lower_price}}</text>元起
						</view>
						<view @click.stop="getTicket" class="get-btn">购票</view>
					</view>
				</view>
			</view>
		</view>
		<view class="no-list" v-else>
			<view class="other-container">
				<image src="https://novelsys.oss-cn-shenzhen.aliyuncs.com/ticket/static/image/temp/noConcert.png" mode=""></image>
				<view class="no-address">
					目前还没有想看的演出
				</view>
			</view>
		</view>
	</view>
	<no-login v-else></no-login>
</template>

<script>
import noLogin from '../login/noLogin.vue'
import { formatDate } from "../../../common/formatDate.js"
import { concernConcert } from "../../../Api/myApi/concernConcert.js";
import { payOrder } from "../../../Api/myApi/payOrder.js";
import backHeader from '../../../components/childheader.vue';
export default {
	data() {
		return {
			loginStatus: false,
			wannaSee: [/* 
				{
					id: 42,
					name: '马天宇演唱会',
					city_name: '重庆市江北区重庆大剧院',
					poster: 'https:\/\/ticket-app.oss-cn-shenzhen.aliyuncs.com\/TICKET\/concerts\/admin\/imgs\/QgghL4gEKbTKVYXL2GqN4B7vFjsruAg4FqgSyLoe.jpeg',
					lower_price: 0,
					discount: 100,
					start_at: 1591372800,
					end_at: 0,
					venue_id: 1,
					venue: {
						id: 1,
						name: '重庆大剧院',
						addr: '重庆市江北区重庆大剧院'
					}
				},
				{
					id: 43,
					name: '那英演唱会',
					city_name: '江西宁都UI哦热我',
					poster: 'https:\/\/ticket-app.oss-cn-shenzhen.aliyuncs.com\/TICKET\/concerts\/admin\/imgs\/1SO7Gr0g7yd9hay6Yz1WfRWrgVHbYcMjS2SJTZos.jpeg',
					lower_price: 0,
					discount: 77,
					start_at: 1591372700,
					end_at: 0,
					venue_id: 2,
					venue: {
						id: 2,
						name: '河北场馆',
						addr: '江西宁都UI哦热我'
					}
				}
			 */],
			noConcert: true
		};
	},
	components: {
		backHeader,
		noLogin
	},
	methods: {
		getTicket(){
			console.log("跳转购票界面")
		},
		goToDetail(id){
			uni.navigateTo({
				url:`/pages/index/yanchudetails/yanchudetails?id=${id}`
			})
		}
	},
	onShow() {
		concernConcert().then(res=>{
			console.log(res)
			this.wannaSee = res[1].data.data;
			if(this.wannaSee.length>0){
				this.noConcert = true;
				this.wannaSee.forEach(item=>{
					item.start_at = formatDate(item.start_at);
					item.end_at = formatDate(item.end_at);
				})
			}else{
				this.noConcert = false;
			}
		})
	},
	onLoad() {
		if(uni.getStorageSync("loginStatus")){
			this.loginStatus = true;
		}
	}
};
</script>

<style lang="less" scoped>
	page{height: 100%;}
	.want-see{
		height: 100%;
		.see-main{
			padding-top: 140upx;
			.concern-item{
				padding: 60upx 40upx 0 40upx;
				display: flex;
				.concern-left{
					flex: 0 185upx;
					margin-right: 50upx;
					image{
						width: 185upx;
						height: 260upx;
						border-radius: 5upx;
					}
				}
				.concern-right{
					flex: 1;
					.right-title{
						color: #212121;
						font-weight: 600;
						font-size: 28upx;
					}
					.right-time{
						padding-top: 12upx;
						color: #999999;
						font-size: 24upx;
					}
					.bottom-wraper{
						display: flex;
						color: #212121;
						justify-content: space-between;
						align-items: flex-end;
						margin-top: 40upx;
						.price{
							font-size: 24upx;
							text{
								font-weight: bold;
								font-size: 36upx;
							}
						}
						.get-btn{
							background: #212121;
							text-align: center;
							width: 120upx;
							height: 60upx;
							border-radius: 10upx;
							color: #fff;
							line-height: 60upx;
							font-weight: 600;
							font-size: 24upx;
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
