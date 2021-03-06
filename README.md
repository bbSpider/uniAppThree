

项目地址：https://gitee.com/bbspider/uni-app-three

**uni-app 的  APP 端导入外部 3d 模型**

@[TOC](目录)

# 概述
使用 uni-app 框架开发  APP，需要导入外部 3d 模型。就参考[这个插件](https://ext.dcloud.net.cn/plugin?id=4445)，就写了这个可以导入外部 3D 模型的案例。

此 demo 只适用于 APP 和 H5，若要满足微信小程序上使用，可以查看我的这篇博客，[点击这里](https://blog.csdn.net/qq_40558766/article/details/105374678?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164154546716781683982640%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=164154546716781683982640&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-3-105374678.nonecase&utm_term=threejs&spm=1018.2226.3001.4450)。



# 使用技术
使用 uni-app 开发 APP 导入外部模型，用到 renderjs，以及 Threejs 技术。

详细文档：

renderjs：[点击这里](https://uniapp.dcloud.io/frame?id=renderjs)

Threejs：[点击这里](http://www.yanhuangxueyuan.com/threejs/docs/index.html)

# 步骤
## 1.下载源码

先从 GitHub 上面下载 threejs 源码

源码链接：https://github.com/mrdoob/three.js

## 2.导入文件

把整个 threejs 源码放入创建好的 uni-app 项目的 static 文件夹里，如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/90bf010f1df14a6b88c9b582806b7599.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA54mb5ZOe5ZOe5p2C6K6w,size_10,color_FFFFFF,t_70,g_se,x_16)
## 3.代码功能

（1）打开 index.vue 文件，在 <template> 标签里加上如下代码，这里是 3d 模型在页面上展示的位置

（2）使用 renderjs 在 js 代码里，在<script> 的标签上加上 lang="renderjs" 以及 module="three" 

（3）导入 threejs 文件，以及 OrbitControls 和 GLTFLoader 文件

OrbitControls：是对 Threejs 的三维场景进行缩放、平移、旋转操作

GLTFLoader：导入 glb 格式模型，若要导入其他格式模型，可尝试在 loaders 目录下加载其他文件

（5）接下来就是创建场景，导入模型以及渲染了

# 代码
## 1.所有代码

```javascript
<template>
	<!-- #ifdef APP-PLUS || H5 -->
	<view id="threeView"></view>
	<!-- #endif -->
	<!-- #ifndef APP-PLUS || H5 -->
	<view>非 APP、H5 环境不支持</view>
	<!-- #endif -->

</template>


<!-- 只能在APP和h5端运行 -->
<script module="three" lang="renderjs">
	const THREE = require('static/three/build/three.min.js')
	
	// OrbitControls 是对 Threejs 的三维场景进行缩放、平移、旋转操作
	import {
		OrbitControls
	} from 'static/three/examples/jsm/controls/OrbitControls.js'

	// 导入 glb 格式模型，若要导入其他格式模型，可尝试在 loaders 目录下加载其他文件
	import {
		GLTFLoader
	} from 'static/three/examples/jsm/loaders/GLTFLoader.js'

	var renderer;
	var scene;
	var camera;
	var controls;

	export default {

		mounted() {
			this.initThree();//加载场景
			this.leadModel();//导入模型
			this.createControls();//控制模型的缩放、平移、旋转操作

		},
		
		methods: {

			createControls() {
				controls = new OrbitControls(camera, renderer.domElement)
			},

			// 导入模型
			leadModel() {
				let loader = new GLTFLoader();

				// 导入本地或者服务器上的模型都可以
				loader.load('static/model/RobotExpressive.glb', function(gltf) {
					// loader.load('https://threejs.org/examples/models/gltf/RobotExpressive/RobotExpressive.glb', function(gltf) {

					scene.add(gltf.scene);

				});

			},

			initThree() {
				// 如果返回的不是未定义，说明threejs成功引入
				console.log('打印场景API', THREE.Scene);

				/* 创建场景对象Scene */
				scene = new THREE.Scene();

				// 环境光
				var ambient = new THREE.AmbientLight(0xffffff);
				scene.add(ambient);

				/*
				    相机设置
				 */
				var width = window.innerWidth; // 窗口宽度
				var height = window.innerHeight; // 高度
				// var width = 300; // 窗口宽度
				// var height = 300; // 高度
				var k = width / height; // 窗口宽高比
				var s = 1000; // 三维场景显示范围控制系数，系数越大，显示的范围越大
				// 创建相机对象（正射投影）
				camera = new THREE.PerspectiveCamera(45, k, 1, 10000);
				camera.position.set(0, 0, 20); //设置相机的摆放位置
				camera.lookAt(new THREE.Vector3(0, 0, 0)); // 控制相机的焦点(镜头)位置，决定相机的朝向（取值为3维坐标对象-THREE.Vector3(x,y,z)）

				/*
				    创建渲染器对象
				 */
				renderer = new THREE.WebGLRenderer({
					antialias: true,
					// alpha: true //设置透明，为true时，背景颜色需要注释掉
				});

				renderer.setSize(width, height); // 设置渲染区域尺寸
				renderer.setClearColor(0XECF1F3, 1); // 设置背景颜色
				const element = document.getElementById('threeView')
				element.appendChild(renderer.domElement); // body元素中插入canvas对象
				// 执行渲染操作，指定场景，相机作为参数
				renderer.render(scene, camera);

				this.render();
			},

			// 动画
			render() {
				let that = this;
				requestAnimationFrame(function() {
					that.render();
				});
				renderer.render(scene, camera); //执行渲染操作
			},

		}
	}
</script>

<style>

</style>

```




