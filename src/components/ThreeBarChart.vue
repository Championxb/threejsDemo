<template>
    <div ref="threeContainer" class="three-container"></div>
</template>
<script setup>
import * as THREE from 'three';
import { geoMercator } from 'd3-geo'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import Stats from 'three/examples/jsm/libs/stats.module.js';
import { Line2 } from 'three/examples/jsm/lines/Line2.js';
import { LineMaterial } from 'three/examples/jsm/lines/LineMaterial.js';
import { LineGeometry } from 'three/examples/jsm/lines/LineGeometry.js';
import { CSS2DRenderer, CSS2DObject } from 'three/examples/jsm/renderers/CSS2DRenderer.js';
import { onMounted, ref } from 'vue';
let scene, camera, renderer;

function init() {
    // 初始化场景、相机和渲染器
    scene = new THREE.Scene();
    scene.background = null; // 设置背景为透明

    camera = new THREE.PerspectiveCamera(85, window.innerWidth / window.innerHeight, 10, 1000);//参数：视野角度，宽高比，近裁剪面，远裁剪面
    camera.position.set(0, 5, 15);

    renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setClearColor(0x000000, 0); // 设置渲染器的背景为透明
    document.body.appendChild(renderer.domElement);

    // 添加光源
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);

    const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
    directionalLight.position.set(5, 5, 5);
    scene.add(directionalLight);

    // 创建柱子
    const barData = [
        { value: 285, total: 500, color: 0xffa500, colorRemaining: 0xffd700 },
        { value: 686, total: 1000, color: 0x00aaff, colorRemaining: 0x87cefa },
        { value: 321, total: 700, color: 0x1e90ff, colorRemaining: 0x87cefa },
        { value: 868, total: 1000, color: 0x0000ff, colorRemaining: 0x1e90ff },
    ];

    barData.forEach((data, index) => {
        // 创建表示值的部分
        const valueHeight = data.value / 100;
        const remainingHeight = data.total / 100 - valueHeight;

        const valueGeometry = new THREE.CylinderGeometry(1, 1, valueHeight, 32);
        const valueMaterial = new THREE.MeshPhongMaterial({ color: data.color });
        const valueMesh = new THREE.Mesh(valueGeometry, valueMaterial);
        valueMesh.position.set(index * 3 - 4.5, valueHeight / 2, 0);
        scene.add(valueMesh);

        // 创建表示剩余部分
        const remainingGeometry = new THREE.CylinderGeometry(1, 1, remainingHeight, 32);
        const remainingMaterial = new THREE.MeshPhongMaterial({ color: data.colorRemaining, transparent: true, opacity: 0.5 });
        const remainingMesh = new THREE.Mesh(remainingGeometry, remainingMaterial);
        remainingMesh.position.set(index * 3 - 4.5, valueHeight + remainingHeight / 2, 0);
        scene.add(remainingMesh);

        // 加载字体并创建文本标签
        // const loader = new THREE.FontLoader();
        // loader.load('https://threejs.org/examples/fonts/helvetiker_regular.typeface.json', function (font) {
        //     const textGeometry = new THREE.TextGeometry(data.value.toString(), {
        //         font: font,
        //         size: 0.5,
        //         height: 0.1,
        //     });
        //     const textMaterial = new THREE.MeshPhongMaterial({ color: 0xffffff });
        //     const text = new THREE.Mesh(textGeometry, textMaterial);
        //     text.position.set(valueMesh.position.x - 0.5, valueHeight + remainingHeight + 0.5, 0);
        //     scene.add(text);
        // });
    });

    // 添加地面
    const planeGeometry = new THREE.PlaneGeometry(20, 20);
    const planeMaterial = new THREE.MeshPhongMaterial({ color: 0x333333 });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);
    plane.rotation.x = -Math.PI / 2;
    plane.position.y = -0.1;
    scene.add(plane);

    animate();
}

function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}

onMounted(() => {
    init();
});
</script>