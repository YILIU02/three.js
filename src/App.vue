<script setup>
// 导入Vue相关API
import { onMounted, onUnmounted, ref } from 'vue'
//导入轨道控制器
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls"
// 导入Three.js库
import * as THREE from 'three'

// 使用ref创建容器引用，用于挂载Three.js渲染器
const container = ref(null)
// 声明Three.js相关变量
let scene, camera, renderer, cube, animationId,axesHelper,controls
const textureLoader = new THREE.TextureLoader();
textureLoader.setCrossOrigin('anonymous');
// 组件挂载生命周期钩子
onMounted(() => {
  initThree()       // 初始化Three.js场景
  setupResizeHandler() // 设置窗口resize监听
  animate()         // 启动动画循环
})

// 组件卸载生命周期钩子
onUnmounted(() => {
  cancelAnimationFrame(animationId) // 停止动画循环
  window.removeEventListener('resize', handleResize) // 移除resize监听
  scene = null // 释放场景内存（Three.js对象需要手动销毁）
})

// 初始化Three.js核心要素
function initThree() {

  // 创建场景（所有3D对象的容器）
  scene = new THREE.Scene()
  
  // 创建透视相机（模拟人眼视角）
  camera = new THREE.PerspectiveCamera(
    75, // 视场角(FOV)，单位是角度
    container.value.clientWidth / container.value.clientHeight, // 宽高比
    0.1, // 近裁剪面
    1000 // 远裁剪面
  )


  // 创建WebGL渲染器（使用抗锯齿优化）
  renderer = new THREE.WebGLRenderer({ antialias: true })
  // 设置渲染器尺寸为容器大小
  renderer.setSize(container.value.clientWidth, container.value.clientHeight)
  // 将渲染器的canvas元素添加到DOM容器
  container.value.appendChild(renderer.domElement)
  
  // 创建立方体几何体（长宽高各为1单位）
  const geometry = new THREE.BoxGeometry(1, 1, 1)
  // 创建基础材质（绿色）
  const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
  // 创建网格对象（组合几何体和材质）
  cube = new THREE.Mesh(geometry, material)
  // 将立方体添加到场景
  scene.add(cube)
    //添加世界坐标辅助器
    axesHelper=new THREE.AxesHelper(5)
  scene.add(axesHelper)
    //添加控制器
controls = new OrbitControls(camera, renderer.domElement);
//设置阻尼，带惯性
controls.enableDamping=true
//设置阻尼系数
controls.dampingFactor=0.05
//设置自动旋转
controls.autoRotate=true
//设置速度
  // 设置相机位置（沿z轴后移5个单位）
  camera.position.z = 5
  camera.position.x=0.5
  camera.position.y=0.5
}

// 动画循环函数
function animate() {
  controls.update()
  // 请求下一帧动画（保持60FPS循环）
  animationId = requestAnimationFrame(animate)
  
  // 更新立方体旋转角度（x轴和y轴各加0.01弧度）
  // cube.rotation.x += 0.01
  // cube.rotation.y += 0.01
  
  // 渲染场景（将3D场景绘制到2D画布）
  renderer.render(scene, camera)
}

// 窗口大小变化处理函数
function handleResize() {
  // 更新相机宽高比
  camera.aspect = container.value.clientWidth / container.value.clientHeight
  // 更新相机投影矩阵（必须调用以应用新的宽高比）
  camera.updateProjectionMatrix()
  // 更新渲染器尺寸
  renderer.setSize(container.value.clientWidth, container.value.clientHeight)
}

// 设置窗口resize事件监听
function setupResizeHandler() {
  window.addEventListener('resize', handleResize)
}
</script>

<template>
  <!-- Three.js容器元素，通过ref绑定到container变量 -->
  <div ref="container" class="three-container"></div>
  <div>111</div>
</template>

<style scoped>
/* 容器样式设置 */
.three-container {
  width: 100vw;     /* 视窗宽度100% */
  height: 100vh;    /* 视窗高度100% */
  position: fixed;  /* 固定定位 */
  top: 0;           /* 顶部对齐 */
  left: 0;          /* 左侧对齐 */
  /* 隐藏溢出内容，确保canvas不会超出视口 */
  overflow: hidden;
}
</style>