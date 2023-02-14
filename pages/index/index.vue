<template>
	<view class="container">
		<view class="leftblock">
			<view class="header">
				<view class="logo">
					<image style="height: 30px" src="/static/logo.png" mode="heightFix"></image>
				</view>
				<view class="title">
					<text style="font-size: 1.5em; color:#adb5bd">电磁数字孪生智慧平台</text>
				</view>
			</view>
			<view class="convas">
				<div id="ros-convas"></div>
			</view>
		</view>
		<view class="rightblock">
			<view class="segment-slector">
				<uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="button"
					activeColor="#4cd964"></uni-segmented-control>
			</view>
			<view class="segment-content">
				<view v-show="current === 0">
					选项卡1的内容
				</view>
				<view v-show="current === 1">
					选项卡2的内容
				</view>
				<view v-show="current === 2">
					选项卡3的内容
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import ROSLIB from 'roslib'
	import * as ROS3D from 'ros3d'
	console.log(ROS3D.Viewer)



	export default {
		data() {
			return {
				items: ['选项1', '选项2', '选项3'],
				current: 0
			};
		},
		onReady() {
			let getWindowInfo = uni.getWindowInfo()

			var ros = new ROSLIB.Ros({
				url: 'ws://192.168.109.130:9090/'
			});
			// Create the main viewer
			var viewer = new ROS3D.Viewer({
				divID: 'ros-convas',
				width: getWindowInfo.windowWidth * 0.7,
				background: "#1E2125",
				height: getWindowInfo.windowHeight - 90,
				antialias: true
			});

			// Setup a client to listen to TFs.
			var tfClient = new ROSLIB.TFClient({
				ros: ros,
				angularThres: 0.01,
				transThres: 0.01,
				rate: 20.0,
				fixedFrame: '/base_link'
			});


			// Setup the map client.
			// var gridClient = new ROS3D.OccupancyGridClient({
			// 	ros: ros,
			// 	tfClient: tfClient,
			// 	rootObject: viewer.scene
			// });

			// Setup the path client.
			var urdfClient = new ROS3D.UrdfClient({
			  ros : ros,
			  tfClient : tfClient,
			  path : 'http://localhost:8080/static/',
			  rootObject : viewer.scene,
			});
			
			var grid = new ROS3D.Grid()
		    viewer.addObject(grid)
			

			uni.onWindowResize((res) => {
				viewer.resize(res.size.windowWidth * 0.7, res.size.windowHeight - 90)
			})


		},
		methods: {
			onClickItem(e) {
				if (this.current != e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		}
	}
</script>

<style>
	.container {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
	}

	.leftblock {
		background: rgb(30, 33, 37);
		display: flex;
		flex-direction: column;
		flex: 1;
		justify-content: flex-start;
		padding: 15px
	}

	.header {
		display: flex;
		flex-direction: row;
		height: 60px;
		align-items: center;
		justify-content: flex-start;
	}

	.logo {
		padding: 0.5em;
	}

	.title {
		padding: 0.5em;
	}

	.convas {
		flex: 1;
		box-shadow: 0 0 1em black;
	}

	.rightblock {
		background: rgb(30, 33, 37);
		display: flex;
		flex-direction: column;
		width: 30%;
		padding: 15px;
		justify-content: flex-start;
	}

	.segment-slector {
		padding-top: 12px;
		padding-bottom: 12px;
		height: 60px;
	}

	.segment-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-start;
		color: #adb5bd
	}
</style>
