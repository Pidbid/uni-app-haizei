<template>
	<view>
		<view>
			<uni-notice-bar show-close="true" show-icon="true" background-color="#ffffff" text="视频均来自网络,请勿相信视频内广告!"></uni-notice-bar>
		</view>
		<view>
			<uni-fab :pattern="pattern" :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction"
			 @trigger="trigger"></uni-fab>
		</view>
		<view><video :src="newmovie" :autoplay="autoplay" id="video1" controls poster="this is a post image link"></video></view>
		<view class="box">
			<QSTabs ref="tabs" :tabs="tabs" animationMode="line3" :current="current" @change="change" activeColor="#fcd217"
			 swiperWidth="750">
			</QSTabs>
		</view>
		<swiper class="swipClass" :current="current" @transition="transition" @animationfinish="animationfinish">
			<swiper-item class="swiper-item" v-for="(item, index) in tabs" :key="index">
				<view class="jishu" v-for="(msg,ii) in allMsg[index]">
					<uni-tag type="warning" inverted="true" :text="msg['title']" @click='playVideo(index,ii)'></uni-tag>
				</view>
			</swiper-item>
		</swiper>
		<uni-popup ref="showshare" type="bottom">
			<view class="uni-share">
				<text class="uni-share-title">快速打开</text>
				<view class="uni-share-content">
					<view v-for="(item, index) in bottomData" :key="index" class="uni-share-content-box">
						<view @click="fastTo(item.name)">
							<view class="uni-share-content-image">
								<image :src="item.icon" class="content-image" mode="widthFix" />
							</view>
							<text class="uni-share-content-text">{{ item.text }}</text>
						</view>
					</view>
				</view>
				<text class="uni-share-btn" @click="cancel('share')">取消</text>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	import QSTabs from '@/components/QS-tabs/QS-tabs.vue';
	import uniNoticeBar from "@/components/uni-notice-bar/uni-notice-bar.vue";
	import uniFab from '@/components/uni-fab/uni-fab.vue';
	import uniTag from "@/components/uni-tag/uni-tag.vue";
	import sortData from '../../data/sortData.js'
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	export default {
		components: {
			QSTabs,
			uniNoticeBar,
			uniFab,
			uniTag,
			uniPopup
		},
		data() {
			return {
				newmovie: "http://cn7.7639616.com/hls/20191117/74b1a65f695497ec585522a5128c7da3/1573976747/index.m3u8",
				playMsg:[],
				tabs: [],
				current: 0,
				tabsHeight: 0,
				dx: 0,
				zjMsg: [],
				allMsg: [],
				horizontal: 'left',
				vertical: 'bottom',
				direction: 'vertical',
				inverted: 'true',
				autoplay: false,
				sgetMsg: [],
				huancunData: sortData,
				list: ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10'],
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#f5a623',
					buttonColor: "#f5a623"
				},
				content: [{
						iconPath: '/static/go_3.png',
						selectedIconPath: '/static/go_3.png',
						text: '快速播放'
					},
					{
						iconPath: '/static/go_2.png',
						selectedIconPath: '/static/go_2.png',
						text: '更新'
					},
					{
						iconPath: '/static/go_1.png',
						selectedIconPath: '/static/go_1.png',
						text: '关于'
					}
				],
				bottomData: [{
						text: '阿拉巴斯坦',
						icon: '../../static/fast/s6.png',
						name: '1'
					},
					{
						text: '司法岛战役228-325',
						icon: '../../static/fast/s1.png',
						name: '2'
					}, {
						text: '恐怖三帆船337-381',
						icon: '../../static/fast/s4.png',
						name: '3'
					},
					{
						text:'推进城',
						icon:'../../static/fast/s3.png',
						name:'4'
					},
					{
						text: '顶上战争459-489',
						icon: '../../static/fast/s2.png',
						name: '5'
					}, {
						text: '鱼人岛战役523-574',
						icon: '../../static/fast/s5.png',
						name: '6'
					}
				]
			}
		},
		onLoad() {
			/*此部分事从网络上获取播放列表
			function qMsg() {
				var a = []
				uni.request({
					url: 'json file link',
					success: function(res) {
						a = res.data
					}
				})
				return a
			}
			var getMsg = []
			try {
				getMsg = uni.getStorageSync('allMsg')
				var xin = qMsg()
				if (getMsg != xin) {
					console.log('检测到更新')
					uni.setStorage({
						key: 'allMsg',
						data: xin
					})
				}
			} catch (e) {
				getMsg = qMsg()
				console.log(123)
			}
			*/
			var oldMsg = this.huancunData
			var getMsg = oldMsg['data']
			var getNew = getMsg['all'][0]['title'] //获取当前更新的最新集数
			this.newmovie = getMsg['all'][0]['link']
			var changdu = (getNew - getNew % 100) / 100
			for (var i = 0; i <= changdu; i++) {
				if (i == changdu) {
					var title = i * 100 + 1 + '-' + getNew
				} else {
					var title = i * 100 + 1 + '-' + (i + 1) * 100
				}
				this.$set(this.tabs, i, String(title)) //实时修改数组
			}
			this.tabs.reverse() //翻转数组，将最新更新的排在前面
			for (var i = 0; i <= changdu; i++) {
				if (i == 0) {
					for (var p = 0; p < getNew % 100; p++) {
						const shipin = getMsg['all'][p]
						this.$set(this.zjMsg, p, shipin)
						this.$set(this.allMsg, i, this.zjMsg)
					}
				} else {
					for (var q = 0; q <= 100; q++) {
						if (q == 0) {
							this.zjMsg = []
						} else {
							const yunsuan = 100 * i - 90 + q
							const shipin = getMsg['all'][yunsuan]
							this.zjMsg.push(shipin)
							//this.$set(this.zjMsg,p,shipin)
							this.$set(this.allMsg, i, this.zjMsg)
						}
					}
				}
			}
		},
		methods: {
			change(index) {
				this.current = index;
			},
			transition({
				detail: {
					dx
				}
			}) { //swiper-item位置改变时触发的事件
				this.$refs.tabs.setDx(dx);
			},
			animationfinish({
				detail: {
					current
				}
			}) { //动画结束时触发的事件
				this.$refs.tabs.setFinishCurrent(current);
				this.current = current;
			},
			playVideo(da, xiao) {
				this.newmovie = this.allMsg[da][xiao]['link']
				var playJishu = this.allMsg[da][xiao]['title']
				console.log(this.allMsg[da][xiao]['link'])
				uni.showToast({
					title: '你正在播放第' + playJishu + '集，请勿相信视频内广告！',
					position: 'bottom',
					success() {
						//var mk = uni.createVideoContext('video1')
						uni.pageScrollTo({
							scrollTop: 0,
							duration: 300
						})
					}
				})

			},
			trigger(e) {
				this.content[e.index].active = !e.item.active;
				//console.log(e.index)
				if (e.index == 0) {
					this.$refs.showshare.open()
				} else if (e.index == 1) {

				} else {

				}
			},
			setInverted() {
				this.inverted = !this.inverted;
			},
			fastTo(a){
				console.log(typeof(a))
				var b = Number(a)
				const c = uni.getStorageSync('allMsg')
				console.log(c)
				switch (b){
					case 1:
						break;
					case 2:
						break;
					case 3:
						break;
					case 4:
						break;
					case 5:
						break;
					default:
						break;
				}
			}
		}
	}
</script>

<style>
	.body {
		/*距离顶部范围应该在88-95范围内*/
		padding-top: 90upx;
		top: var(--status-bar-height);
	}

	.tanchu {
		z-index: 999;
	}

	video {
		width: 100%;
		top: 0;
		position: sticky;
	}

	.swipClass {
		height: 900px;
	}

	.box {
		width: 100%;
		position: sticky;
		top: 0;
		z-index: 10;
		background-color: white;
	}

	.jishu {
		float: left;
		width: 20%;
		padding: 5px 10px 0px 10px;
		box-sizing: border-box;
		background-clip: content-box;
	}

	.anniu {}

	/* 底部分享 */
	.uni-share {
		/* #ifndef APP-NVUE */
		display: flex;
		flex-direction: column;
		/* #endif */
		background-color: #fff;
		border-radius: 40rpx;
	}

	.uni-share-title {
		line-height: 60rpx;
		font-size: 30rpx;
		padding: 15rpx 0;
		text-align: center;
	}

	.uni-share-content {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: center;
		padding: 15px;
	}

	.uni-share-content-box {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		width: 200rpx;
	}

	.uni-share-content-image {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		justify-content: center;
		align-items: center;
		width: 75rpx;
		height: 75rpx;
		overflow: hidden;
		border-radius: 10rpx;
	}

	.content-image {
		width: 75rpx;
		height: 75rpx;
	}

	.uni-share-content-text {
		font-size: 28rpx;
		color: #333;
		padding-top: 5px;
		padding-bottom: 10px;
	}

	.uni-share-btn {
		height: 90rpx;
		line-height: 90rpx;
		font-size: 16px;
		border-top-color: #f5f5f5;
		border-top-width: 1px;
		border-top-style: solid;
		text-align: center;
		color: #666;
	}
</style>
