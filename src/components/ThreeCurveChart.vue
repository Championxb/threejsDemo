<template>
    <div ref="threeContainer" class="three-container"></div>
</template>
<script setup>
import * as THREE from 'three';
import { onMounted, ref, onBeforeUnmount } from 'vue';
import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js';
import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry.js';

let scene, camera, renderer;
const WIDTH = 500;
const HEIGHT = 400;
const threeContainer = ref(null);
const init = () => {
    // 设置场景、相机和渲染器
    scene = new THREE.Scene();
    camera = new THREE.PerspectiveCamera(75, WIDTH / HEIGHT, 0.1, 1000);
    renderer = new THREE.WebGLRenderer();
    renderer.setSize(WIDTH, HEIGHT);
    threeContainer.value.appendChild(renderer.domElement);

    // 添加光源
    const light = new THREE.PointLight(0xffffff, 1, 100);
    light.position.set(50, 50, 50);
    scene.add(light);

    // 定义曲线上的点
    const points1 = [];
    const points2 = [];
    for (let i = 0; i <= 100; i++) {
        const x = i / 10;
        points1.push(new THREE.Vector3(x, Math.sin(x), 0));
        points2.push(new THREE.Vector3(x, Math.sin(x) + 1, 0)); // 第二条曲线向上偏移
    }

    // 创建波浪曲线
    const curve1 = new THREE.CatmullRomCurve3(points1);
    const curve2 = new THREE.CatmullRomCurve3(points2);

    const lineMaterial = new THREE.LineBasicMaterial({ color: 0x0000ff });

    const geometry1 = new THREE.BufferGeometry().setFromPoints(curve1.getPoints(100));
    const line1 = new THREE.Line(geometry1, lineMaterial);
    scene.add(line1);

    const geometry2 = new THREE.BufferGeometry().setFromPoints(curve2.getPoints(100));
    const line2 = new THREE.Line(geometry2, lineMaterial);
    scene.add(line2);

    // 创建渐变填充材质
    const fillMaterial = new THREE.ShaderMaterial({
        uniforms: {
            color1: { value: new THREE.Color(0x00ff00) },
            color2: { value: new THREE.Color(0x0000ff) }
        },
        vertexShader: `
            varying vec2 vUv;
            void main() {
            vUv = uv;
            gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
            }
        `,
        fragmentShader: `
            uniform vec3 color1;
            uniform vec3 color2;
            varying vec2 vUv;
            void main() {
                gl_FragColor = vec4(mix(color1, color2, vUv.y), 1.0);
            }
        `,
        side: THREE.DoubleSide,
        transparent: true
    });

    // 创建填充区域的几何体
    const fillPoints = [];
    fillPoints.push(...points1);
    fillPoints.push(...points2.reverse());

    const fillGeometry = new THREE.BufferGeometry().setFromPoints(fillPoints);
    const fillMesh = new THREE.Mesh(fillGeometry, fillMaterial);
    scene.add(fillMesh);

    // 设置相机位置
    camera.position.z = 10;

    animate();
}

// 渲染循环
function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}

onMounted(() => {
    init();
});
</script>