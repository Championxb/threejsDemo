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
    // 初始化场景、相机和渲染器
    renderer = new THREE.WebGLRenderer();
    renderer.setSize(WIDTH, HEIGHT);
    renderer.setClearColor(0x000000, 0); // 设置渲染器的背景为透明
    threeContainer.value.appendChild(renderer.domElement);

    camera = new THREE.PerspectiveCamera(75, WIDTH / HEIGHT, 0.1, 1000);
    camera.position.z = 5;


    // 添加光源
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);

    const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
    directionalLight.position.set(5, 5, 5);
    scene.add(directionalLight);


    animate();
}



// 创建曲线
const curve = new THREE.SplineCurve([
    new THREE.Vector2(-5, 0),
    new THREE.Vector2(-2.5, 2.5),
    new THREE.Vector2(0, 0),
    new THREE.Vector2(2.5, -2.5),
    new THREE.Vector2(5, 0)
]);

const points = curve.getPoints(50);
const geometry = new THREE.BufferGeometry().setFromPoints(points);

const material = new THREE.LineBasicMaterial({ color: 0x0000ff });

// 创建线条并添加到场景中
const line = new THREE.Line(geometry, material);
scene.add(line);

// 创建文本精灵
function createTextCanvas(text, color, fontSize) {
    const canvas = document.createElement('canvas');
    const context = canvas.getContext('2d');
    context.font = `${fontSize}px Arial`;
    context.fillStyle = color;
    context.fillText(text, 0, fontSize);
    return canvas;
}

function createTextTexture(text, color, fontSize) {
    const canvas = createTextCanvas(text, color, fontSize);
    const texture = new THREE.CanvasTexture(canvas);
    return texture;
}

function createTextSprite(text, color, fontSize) {
    const texture = createTextTexture(text, color, fontSize);
    const material = new THREE.SpriteMaterial({ map: texture });
    const sprite = new THREE.Sprite(material);
    sprite.scale.set(2, 1, 1);
    return sprite;
}

const textSprite = createTextSprite('预警值', '#FF0000', 50);
textSprite.position.set(0, 1.5, 0);
scene.add(textSprite);

// 渲染循环
function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}


onMounted(() => {
    init();
}
</script>