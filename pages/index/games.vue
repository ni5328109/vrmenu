<template>
	<view class="index-page">
		<swiper class="index-swiper" indicator-dots="true" autoplay="true" indicator-active-color="#ffffff">
			<swiper-item class="swiper-item"><image src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-jiuyuevr/b19d91a0-9264-11ea-8bd0-2998ac5bbf7e.png"></image></swiper-item>
			<swiper-item class="swiper-item"><image src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-jiuyuevr/b19e06d0-9264-11ea-9e8b-05a3242b26f2.jpg"></image></swiper-item>
		</swiper>
		<scroll-view class="scroll-type" scroll-x="true">
			<view class="type-items">
				<view v-for="(item, index) in types" :key="index" :class="currentType == item ? 'item-select' : ''" @click="chooseType(item)">{{ item }}</view>
			</view>
		</scroll-view>
		<view class="games game-bg">
			<view class="game-item" v-for="(item, index) in items" :key="index" @click="jumpToDetail(item)">
				<view class="game-left"><image :src="item.src"></image></view>
				<view class="game-right">
					<view class="right-title">{{ item.title }}</view>
					<view class="right-context">{{ item.context }}</view>
				</view>
				<view class="want-play" :class="item.isWant ? 'no-want' : ''" @click.stop="wantPlay(item)">{{ item.isWant == 1 ? '不想玩' : item.isWant == 2 ? '已玩' : '想玩' }}</view>
			</view>
		</view>
		<view style="height: 1vw;background: #1d2b3c;position: relative;top: 1vw;"></view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			types: ['新手推荐', '射击游戏', '动作游戏', '刺激游戏'],
			currentType: '新手推荐',
			changeData: 0,
			items: [],
			games: []
		};
	},
	methods: {
		jumpToDetail(item) {
			uni.navigateTo({
				url: './detail?id=' + item._id + '&title=' + item.title
			});
		},
		chooseType(type) {
			this.currentType = type;
			let items = [];
			for (let game of this.games) {
				if (game.type === type) {
					items.push(game);
				}
			}
			this.items = items;
		},
		wantPlay(item) {
			if (item.isWant === 2) {
				return;
			}
			item.isWant == 1 ? (item.isWant = 0) : (item.isWant = 1);
			this.changeData += 1;
			if (item.isWant === 1) {
				let ids = wx.getStorageSync('want') || [];
				ids.push(item.id);
				wx.setStorageSync('want', ids);
			} else {
				let ids = wx.getStorageSync('want');
				let newIds = [];
				for (let id of ids) {
					if (id != item.id) {
						newIds.push(id);
					}
				}
				wx.setStorageSync('want', newIds);
			}
		}
	},
	onShow() {
		let wantIds = wx.getStorageSync('want') || [];
		let haveIds = wx.getStorageSync('have') || [];
		this.$cloud('games', { type: 'search' }, true).then(res => {
			this.games = res
			for (let game of res) {
				if (wantIds.indexOf(game.id) >= 0) {
					game.isWant = 1;
				}
				if (haveIds.indexOf(game.id) >= 0) {
					game.isWant = 2;
				}
			}
			this.chooseType('新手推荐');
		});
	}
};
</script>

<style lang="scss">
.index-page {
	width: 100vw;
	background: #1d2b3c;
	.index-swiper {
		width: 100vw;
		height: 50vw;
		.swiper-item {
			width: 100vw;
			height: 50vw;
			image {
				width: 100vw;
				height: 50vw;
			}
		}
	}
	.scroll-type {
		height: 8vw;
		line-height: 8vw;
		width: 100vw;
		background: #1d2b3c;
		position: sticky;
		top: 0;
		z-index: 10;
		.type-items {
			view {
				width: 20vw;
				text-align: center;
				display: inline-block;
				color: #2a475e;
				border-top-left-radius: 1vw;
				border-top-right-radius: 1vw;
				font-size: 4vw;
			}
			.item-select {
				background: #2a475e;
				color: #ffffff;
			}
		}
	}
}
</style>
