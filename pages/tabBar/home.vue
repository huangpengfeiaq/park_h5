<template>
	<view>
		<!-- 状态栏 -->
		<view class="status" :style="{ position: headerPosition, top: statusTop, opacity: afterHeaderOpacity }"></view>
		<!-- 顶部导航栏 -->
		<view class="header" :style="{ position: headerPosition, top: headerTop, opacity: afterHeaderOpacity }">
			<!-- 定位城市 -->
			<view class="addr" @tap="toPark">
				<view class="icon location"></view>
				{{ park.name }}
			</view>
			<!-- 搜索框 -->
			<!-- <view class="input-box">
				<input placeholder="默认关键字" placeholder-style="color:#c0c0c0;" @tap="toSearch()" />
				<view class="icon search"></view>
			</view> -->
			<!-- 右侧图标按钮 -->
			<view class="icon-btn">
				<!-- <view class="icon yuyin"></view> -->
				<view class="icon tongzhi" @tap="toMsg"></view>
			</view>
		</view>
		<!-- 占位 -->
		<view class="place"></view>
		<!-- 轮播图 -->
		<view class="swiper" v-if="swiperList.length > 0">
			<view class="swiper-box">
				<swiper circular="true" autoplay="true" @change="swiperChange">
					<swiper-item v-for="swiper in swiperList" :key="swiper.id"><image :src="swiper.picture" @tap="toSwiper(swiper)"></image></swiper-item>
				</swiper>
				<view class="indicator"><view class="dots" v-for="(swiper, index) in swiperList" :class="[currentSwiper >= index ? 'on' : '']" :key="index"></view></view>
			</view>
		</view>
		<!-- 分类列表 -->
		<view class="category-list" v-if="categoryList.length > 0">
			<view class="category" v-for="(row, index) in categoryList" :key="index" @tap="toCategory(row)">
				<view class="img"><image :src="row.icon"></image></view>
				<view class="text">{{ row.name }}</view>
			</view>
		</view>
		<!-- 广告图 -->
		<!-- <view class="banner"><image src="../../static/img/banner.jpg"></image></view> -->
		<!-- 活动区 -->
		<!-- 	<view class="promotion">
			<view class="text">优惠专区</view>
			<view class="list">
				<view class="column" v-for="(row, index) in Promotion" @tap="toPromotion(row)" :key="index">
					<view class="top">
						<view class="title">{{ row.title }}</view>
						<view class="countdown" v-if="row.countdown">
							<view>{{ row.countdown.h }}</view>
							:
							<view>{{ row.countdown.m }}</view>
							:
							<view>{{ row.countdown.s }}</view>
						</view>
					</view>
					<view class="left">
						<view class="ad">{{ row.ad }}</view>
						<view class="into">点击进入</view>
					</view>
					<view class="right"><image :src="row.img"></image></view>
				</view>
			</view>
		</view> -->
		<!-- 商品列表 -->
		<view class="goods-list" v-if="productList.length > 0">
			<view class="title">
				<image src="../../static/img/hua.png"></image>
				猜你喜欢
				<image src="../../static/img/hua.png"></image>
			</view>
			<view class="product-list">
				<view class="product" v-for="product in productList" :key="product.houseId" @tap="toGoods(product)">
					<image mode="widthFix" :src="product.cover"></image>
					<view class="name">{{ product.location }}：{{ product.introduction }}</view>
					<view class="info">
						<view class="price">￥{{ product.price }}/月</view>
						<view class="slogan">{{ product.remark }}</view>
					</view>
				</view>
			</view>
			<view class="loading-text">{{ loadingText }}</view>
		</view>
		<!--二维码-->
		<view class="park">
			<!-- 占位 -->
			<view class="place20"></view>
			<image class="rrr" src="../../static/img/temp/erweima.jpg" mode="aspectFit"></image>
			<view class="ttt">长按关注园服大师公众号，随时随地关注园区最新动态</view>

			<!-- 占位 -->
			<view class="place20"></view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			park: {
				id: 0,
				name: '园区加载中，请稍等。。',
				logo: 'http://obs-312.obs.cn-east-2.myhuaweicloud.com/20190426206c5f6d08.jpg',
				location: '330108',
				address: '聚业路28号',
				sort: 0,
				introduction: '环境好，风景优美',
				createBy: '黄鹏飞2号',
				createDate: '2019-04-19 21:04:03',
				updateBy: '黄鹏飞2号',
				updateDate: '2019-04-26 20:37:44',
				status: 1
			},
			afterHeaderOpacity: 1, //不透明度
			headerPosition: 'fixed',
			headerTop: null,
			statusTop: null,
			currentSwiper: 0,
			// 轮播图片
			swiperList: [],
			// 分类菜单
			categoryList: [],
			Promotion: [],
			//猜你喜欢列表
			productList: [],
			loadingText: '到底了'
			// loadingText: '正在加载...'
		};
	},
	onPageScroll(e) {
		//兼容iOS端下拉时顶部漂移
		this.headerPosition = e.scrollTop >= 0 ? 'fixed' : 'absolute';
		this.headerTop = e.scrollTop >= 0 ? null : 0;
		this.statusTop = e.scrollTop >= 0 ? null : -this.statusHeight + 'px';
	},
	//下拉刷新，需要自己在page.json文件中配置开启页面下拉刷新 "enablePullDownRefresh": true
	onPullDownRefresh() {
		setTimeout(function() {
			uni.stopPullDownRefresh();
		}, 1000);
	},
	//上拉加载，需要自己在page.json文件中配置"onReachBottomDistance"
	onReachBottom() {
		// uni.showToast({ title: '触发上拉加载' });
		let len = this.productList.length;
		// if (len >= 2) {
		// 	this.loadingText = '到底了';
		// 	return false;
		// }
		// // 演示,随机加入商品,生成环境请替换为ajax请求
		// // let end_goods_id = this.productList[len - 1].goods_id;
		// for (let i = 1; i <= 3; i++) {
		// 	// let goods_id = end_goods_id + i;
		// 	let p = this.productList[i - 1];
		// 	this.productList.push(p);
		// }
	},
	onLoad(option) {
		if (option.parkId !== undefined) {
			this.park.id = option.parkId;
			uni.request({
				url: this.$tempUrl + 'park/selectByPrimaryKey',
				data: { id: option.parkId },
				method: 'GET',
				success: res => {
					const data = res.data.data;
					this.park = data;
					this.wxfw();
					uni.setStorageSync('park', data);
				}
			});
		} else {
			try {
				this.park = uni.getStorageSync('park');
				this.wxfw();
				const value = uni.getStorageSync('park');
				if (value) {
					console.log(value);
				} else {
					uni.reLaunch({
						url: '../login/login'
					});
				}
			} catch (e) {
				console.log('fail' + e);
				uni.reLaunch({
					url: '../login/login'
				});
			}
		}
		//轮播图
		uni.request({
			url: this.$tempUrl + 'public/listSlideshow',
			data: {
				parkId: this.park.id,
				pageNum: '1',
				pageSize: '99'
			},
			method: 'GET',
			success: res => {
				this.swiperList = res.data.content;
			}
		});
		//分类菜单
		uni.request({
			url: this.$tempUrl + 'app/selectListByParkId',
			data: {
				parkId: this.park.id,
				pageNum: '1',
				pageSize: '99'
			},
			method: 'GET',
			success: res => {
				this.categoryList = res.data.content;
			}
		});
		//猜你喜欢
		uni.request({
			url: this.$tempUrl + 'public/listHouse',
			data: {
				parkId: this.park.id,
				pageNum: '1',
				pageSize: '99'
			},
			method: 'GET',
			success: res => {
				this.productList = res.data.content;
			}
		});
		// #ifdef APP-PLUS
		this.statusHeight = plus.navigator.getStatusbarHeight();
		// #endif
		//开启定时器
		this.Timer();
		//加载活动专区
		this.loadPromotion();
	},
	methods: {
		wxfw() {
			var wx = require('jweixin-module');
			// 获取签名
			uni.request({
				url: this.$tempUrl + '/wechat/config',
				method: 'GET',
				success: res => {
					wx.config({
						debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
						appId: res.data.appId, // 必填，公众号的唯一标识
						timestamp: res.data.timestamp, // 必填，生成签名的时间戳
						nonceStr: res.data.nonceStr, // 必填，生成签名的随机串
						signature: res.data.signature, // 必填，签名
						jsApiList: ['onMenuShareAppMessage', 'onMenuShareTimeline'] // 必填，需要使用的JS接口列表
					});
					// 此部分为微信分享
					let config = {
						title: this.park.name, // 分享标题
						desc: this.park.introduction, // 分享描述
						//link: 'http://www.yuanfudashi.com:80/#/pages/tabBar/home?parkId=' + this.park.id, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
						link: 'http://www.yuanfudashi.com/share.html?parkId=' + this.park.id,
						imgUrl: this.park.logo,
						success: function() {
							console.log('已分享');
							console.log(success); // 用户点击了分享后执行的回调函数
						},
						cancel: function() {
							console.log(failf);
						}
					};
					wx.ready(() => {
						wx.onMenuShareAppMessage(config);
						wx.onMenuShareTimeline(config);
						// wx.updateAppMessageShareData(config);
						// wx.updateTimelineShareData(config);
					});
					wx.error(function(res) {
						console.log('wx err', res);
						//可以更新签名
					});
				}
			});
		},
		// 选择园区
		toPark(park) {
			uni.reLaunch({
				url: '../login/login'
			});
		},
		//加载Promotion 并设定倒计时,,实际应用中应该是ajax加载此数据。
		loadPromotion() {
			let cutTime = new Date();
			let yy = cutTime.getFullYear(),
				mm = cutTime.getMonth() + 1,
				dd = cutTime.getDate();
			let tmpcountdown = yy + '/' + mm + '/' + dd + ' 23:59:59';
			let tmpPromotion = [
				{
					title: '整点秒杀',
					ad: '整天秒杀专区',
					img: '../../static/img/s1.jpg',
					countdown: false
				},
				{
					title: '限时抢购',
					ad: '每天23点上线',
					img: '../../static/img/s2.jpg',
					countdown: tmpcountdown
				} //countdown为目标时间，程序会获取当前时间倒数
			];
			//检查倒计时
			for (let i = 0; i < tmpPromotion.length; i++) {
				let row = tmpPromotion[i];
				if (row.countdown) {
					let h = '00',
						m = '00',
						s = '00';
					let currentTime = new Date();
					let cutoffTime = new Date(tmpcountdown);
					if (!(currentTime >= cutoffTime)) {
						let countTime = parseInt((cutoffTime.getTime() - currentTime.getTime()) / 1000);
						h = parseInt(countTime / 3600);
						m = parseInt((countTime % 3600) / 60);
						s = countTime % 60;
						h = h < 10 ? '0' + h : h;
						m = m < 10 ? '0' + m : m;
						s = s < 10 ? '0' + s : s;
					}
					tmpPromotion[i].countdown = { h: h, m: m, s: s };
				}
			}
			this.Promotion = tmpPromotion;
		},
		//定时器
		Timer() {
			setInterval(() => {
				if (this.Promotion.length > 0) {
					for (let i = 0; i < this.Promotion.length; i++) {
						let row = this.Promotion[i];
						if (row.countdown) {
							if (!(row.countdown.h == 0 && row.countdown.m == 0 && row.countdown.s == 0)) {
								if (row.countdown.s > 0) {
									row.countdown.s--;
									row.countdown.s = row.countdown.s < 10 ? '0' + row.countdown.s : row.countdown.s;
								} else if (row.countdown.m > 0) {
									row.countdown.m--;
									row.countdown.m = row.countdown.m < 10 ? '0' + row.countdown.m : row.countdown.m;
									row.countdown.s = 59;
								} else if (row.countdown.h > 0) {
									row.countdown.h--;
									row.countdown.h = row.countdown.h < 10 ? '0' + row.countdown.h : row.countdown.h;
									row.countdown.m = 59;
									row.countdown.s = 59;
								}
								this.Promotion[i].countdown = row.countdown;
							}
						}
					}
				}
			}, 1000);
		},
		//消息列表
		toMsg() {
			uni.navigateTo({
				url: '../goods/introduction?parkId=' + this.park.id
			});
		},
		//搜索跳转
		toSearch() {
			uni.showToast({ title: '建议跳转到新页面做搜索功能' });
		},
		//轮播图跳转
		toSwiper(e) {
			uni.showToast({ title: '资讯id：' + e.informationId, icon: 'none' });
			uni.navigateTo({
				url: '../goods/information?informationId=' + e.informationId
			});
		},
		//分类跳转
		toCategory(e) {
			//uni.showToast({title: e.name,icon:"none"});
			// console.log(e);
			if (e.name === '空间出租') {
				uni.navigateTo({
					url: '../goods/zffw?parkId=' + this.park.id + '&name=' + e.name
				});
			} else if (e.name === '入驻企业') {
				uni.navigateTo({
					url: '../goods/qyrz?parkId=' + this.park.id + '&name=' + e.name
				});
			} else if (e.name === '宽带电话') {
				uni.navigateTo({
					url: '../goods/kddh?parkId=' + this.park.id + '&name=' + e.name
				});
			} else {
				uni.navigateTo({
					url: '../goods/goods?appId=' + e.id + '&parkId=' + this.park.id
				});
			}
		},
		//推荐商品跳转
		toPromotion(e) {
			uni.showToast({ title: e.title, icon: 'none' });
		},
		//商品跳转
		toGoods(e) {
			// uni.showToast({ title: '商品' + e.goods_id, icon: 'none' });
			uni.navigateTo({
				url: '../goods/zffw?parkId=' + this.park.id + '&name=' + '空间出租'
			});
		},
		//轮播图指示器
		swiperChange(event) {
			this.currentSwiper = event.detail.current;
		}
	}
};
</script>
<style lang="scss">
page {
	position: relative;
	background: white;
}
@font-face {
	font-family: 'HMfont-home';
	src: url('data:application/x-font-woff2;charset=utf-8;base64,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')
		format('woff2');
}
.icon {
	font-family: 'HMfont-home' !important;
	font-size: 60upx;
	font-style: normal;
	color: #000000;
	&.yuyin {
		&:before {
			content: '\e64e';
		}
	}
	&.tongzhi {
		&:before {
			content: '\e729';
		}
	}
	&.search {
		&:before {
			content: '\e628';
		}
	}
	&.location {
		&:before {
			content: '\e611';
		}
	}
	&.xia {
		&:before {
			content: '\e689';
		}
	}
}
.pullDown-effects {
	position: fixed;
	//top: calc(100upx - 36vw);
	top: 0;
	z-index: 9;
	width: 100%;
	height: 36vw;
	/*  #ifdef  APP-PLUS  */
	padding-top: var(--status-bar-height);
	/*  #endif  */
	image {
		width: 100%;
		height: 36vw;
	}
}
.status {
	width: 100%;
	height: 0;
	position: fixed;
	z-index: 10;
	background-color: #fff;
	top: 0;
	/*  #ifdef  APP-PLUS  */
	height: var(--status-bar-height); //覆盖样式
	/*  #endif  */
}
.header {
	width: 92%;
	padding: 0 4%;
	height: 100upx;
	display: flex;
	align-items: center;
	position: fixed;
	top: 0;
	z-index: 10;
	background-color: #fff;

	/*  #ifdef  APP-PLUS  */
	top: var(--status-bar-height);
	/*  #endif  */

	.addr {
		width: 520upx;
		height: 60upx;
		flex-shrink: 0;
		display: flex;
		align-items: center;
		font-size: 28upx;
		.icon {
			height: 60upx;
			margin-right: 5upx;
			display: flex;
			align-items: center;
			font-size: 42upx;
			color: #ffc50a;
		}
	}
	.input-box {
		width: 100%;
		height: 60upx;
		background-color: #f5f5f5;
		border-radius: 30upx;
		position: relative;
		display: flex;
		align-items: center;
		.icon {
			display: flex;
			align-items: center;
			position: absolute;
			top: 0;
			right: 0;
			width: 60upx;
			height: 60upx;
			font-size: 34upx;
			color: #c0c0c0;
		}
		input {
			padding-left: 28upx;
			height: 28upx;
			font-size: 28upx;
		}
	}
	.icon-btn {
		width: 120upx;
		height: 60upx;
		flex-shrink: 0;
		display: flex;
		.icon {
			width: 60upx;
			height: 60upx;
			display: flex;
			justify-content: flex-end;
			align-items: center;
			font-size: 42upx;
		}
	}
}
.place {
	background-color: #ffffff;
	height: 100upx;
	/*  #ifdef  APP-PLUS  */
	margin-top: var(--status-bar-height);
	/*  #endif  */
}
.swiper {
	width: 100%;
	margin-top: 10upx;
	display: flex;
	justify-content: center;
	.swiper-box {
		width: 92%;
		height: 30.7vw;

		overflow: hidden;
		border-radius: 15upx;
		box-shadow: 0upx 8upx 25upx rgba(0, 0, 0, 0.2);
		//兼容ios，微信小程序
		position: relative;
		z-index: 1;
		swiper {
			width: 100%;
			height: 30.7vw;
			swiper-item {
				image {
					width: 100%;
					height: 30.7vw;
				}
			}
		}
		.indicator {
			position: absolute;
			bottom: 20upx;
			left: 20upx;
			background-color: rgba(255, 255, 255, 0.4);
			width: 150upx;
			height: 5upx;
			border-radius: 3upx;
			overflow: hidden;
			display: flex;
			.dots {
				width: 100%;
				&.on {
					background-color: rgba(255, 255, 255, 1);
				}
			}
		}
	}
}

.category-list {
	width: 92%;
	margin: 0 4%;
	padding: 0 0 30upx 0;
	border-bottom: solid 2upx #f6f6f6;
	display: flex;
	justify-content: space-between;
	flex-wrap: wrap;
	.category {
		width: 25%;
		margin-top: 50upx;
		display: flex;
		flex-wrap: wrap;
		.img {
			width: 100%;
			display: flex;
			justify-content: center;
			image {
				width: 12vw;
				height: 12vw;
			}
		}
		.text {
			margin-top: 16upx;
			width: 100%;
			display: flex;
			justify-content: center;
			font-size: 24upx;
			color: #3c3c3c;
		}
	}
}
.banner {
	width: 92%;
	margin: 40upx 4%;
	image {
		width: 100%;
		height: 20vw;
		border-radius: 10vw;
		box-shadow: 0upx 5upx 25upx rgba(0, 0, 0, 0.3);
	}
}
.promotion {
	width: 92%;
	margin: 0 4%;
	.text {
		width: 100%;
		height: 60upx;
		font-size: 34upx;
		font-weight: 600;
		margin-top: -10upx;
	}
	.list {
		width: 100%;
		display: flex;
		justify-content: space-between;
		.column {
			width: 43%;
			padding: 15upx 3%;
			background-color: #ebf9f9;
			border-radius: 10upx;
			overflow: hidden;
			display: flex;
			justify-content: space-between;
			flex-wrap: wrap;
			.top {
				width: 100%;
				height: 40upx;
				display: flex;
				align-items: center;
				margin-bottom: 5upx;
				.title {
					font-size: 30upx;
				}
				.countdown {
					margin-left: 20upx;
					display: flex;
					height: 40upx;
					display: flex;
					align-items: center;
					font-size: 20upx;
					view {
						height: 30upx;
						background-color: #f06c7a;
						display: flex;
						justify-content: center;
						align-items: center;
						color: #fff;
						border-radius: 4upx;
						margin: 0 4upx;
						padding: 0 2upx;
					}
				}
			}
			.left {
				width: 50%;
				height: 18.86vw;
				display: flex;
				flex-wrap: wrap;
				align-content: space-between;
				.ad {
					margin-top: 5upx;
					width: 100%;
					font-size: 22upx;
					color: #acb0b0;
				}
				.into {
					width: 100%;
					font-size: 24upx;
					color: #aaa;
					margin-bottom: 5upx;
				}
			}
			.right {
				width: 18.86vw;
				height: 18.86vw;
				image {
					width: 18.86vw;
					height: 18.86vw;
				}
			}
		}
	}
}
.goods-list {
	padding-bottom: 10upx;
	border-bottom: solid 2upx #f6f6f6;
	// background-color: #f4f4f4;
	.title {
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 80upx;
		color: #f47825;
		font-size: 30upx;
		margin-top: 10upx;
		image {
			width: 30upx;
			height: 30upx;
		}
	}
	.loading-text {
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 60upx;
		color: #979797;
		font-size: 24upx;
	}
	.product-list {
		width: 92%;
		padding: 0 4% 3vw 4%;
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
		.product {
			width: 48%;
			border-radius: 20upx;
			background-color: #fff;
			margin: 0 0 15upx 0;
			box-shadow: 0upx 5upx 25upx rgba(0, 0, 0, 0.1);
			image {
				width: 100%;
				border-radius: 20upx 20upx 0 0;
			}
			.name {
				width: 92%;
				padding: 10upx 4%;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
				text-align: justify;
				overflow: hidden;
				font-size: 25upx;
			}
			.introduction {
				width: 92%;
				padding: 10upx 4%;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
				text-align: justify;
				overflow: hidden;
				font-size: 25upx;
			}
			.info {
				display: flex;
				justify-content: space-between;
				align-items: flex-end;
				width: 92%;
				padding: 10upx 4% 10upx 4%;

				.price {
					color: #e65339;
					font-size: 30upx;
					font-weight: 600;
				}
				.slogan {
					color: #807c87;
					font-size: 24upx;
				}
			}
		}
	}
}
.park {
	padding-left: 15upx;
	padding-right: 15upx;
	text-align: center;
	.rrr {
		padding-top: 20upx;
		height: 230upx;
	}
	.ttt {
		color: red;
		font-size: 22upx;
	}
	.place20 {
		background-color: #ffffff;
		height: 30upx;
	}
}
</style>
