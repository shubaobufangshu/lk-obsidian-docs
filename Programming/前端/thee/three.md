\# Three.js 完整入门教程（2026最新版）

Three.js 是一个基于 WebGL 的 JavaScript 3D 库，它封装了复杂的 WebGL 底层 API，让开发者能够轻松在浏览器中创建交互式 3D 体验。本教程从基础概念到实战项目，循序渐进，代码均基于 Three.js r164 版本。



\## 一、环境搭建

\### 1.1 快速引入（CDN方式）

最简单的入门方式，无需构建工具：

```html

<!DOCTYPE html>

<html lang="zh-CN">

<head>

&nbsp;   <meta charset="UTF-8">

&nbsp;   <meta name="viewport" content="width=device-width, initial-scale=1.0">

&nbsp;   <title>Three.js 入门</title>

&nbsp;   <style>

&nbsp;       \* { margin: 0; padding: 0; }

&nbsp;       canvas { display: block; } /\* 消除滚动条 \*/

&nbsp;   </style>

</head>

<body>

&nbsp;   <!-- 引入Three.js核心库 -->

&nbsp;   <script src="https://cdn.jsdelivr.net/npm/three@0.164.1/build/three.min.js"></script>

&nbsp;   <!-- 引入常用扩展（按需添加） -->

&nbsp;   <script src="https://cdn.jsdelivr.net/npm/three@0.164.1/examples/js/controls/OrbitControls.js"></script>

&nbsp;   <script src="https://cdn.jsdelivr.net/npm/three@0.164.1/examples/js/loaders/GLTFLoader.js"></script>

&nbsp;   

&nbsp;   <script>

&nbsp;       // 你的Three.js代码写在这里

&nbsp;   </script>

</body>

</html>

```



\### 1.2 现代开发环境（Vite + npm）

推荐用于生产项目：

```bash

\# 创建项目

npm create vite@latest threejs-demo -- --template vanilla

cd threejs-demo



\# 安装依赖

npm install three

npm install -D @types/three # TypeScript支持（可选）



\# 启动开发服务器

npm run dev

```



在 `main.js` 中引入：

```javascript

import \* as THREE from 'three';

import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

```



\## 二、核心概念：Three.js 三大件

任何 Three.js 应用都离不开这三个基础组件：

\- \*\*场景(Scene)\*\*：3D世界的容器，所有物体、灯光、相机都要添加到场景中

\- \*\*相机(Camera)\*\*：定义了我们观察3D世界的视角

\- \*\*渲染器(Renderer)\*\*：将场景和相机的内容渲染到浏览器的canvas上



\## 三、第一个3D应用：旋转立方体

```javascript

// 1. 创建场景

const scene = new THREE.Scene();

scene.background = new THREE.Color(0x87CEEB); // 设置天空蓝背景



// 2. 创建相机

// PerspectiveCamera(视场角, 宽高比, 近裁剪面, 远裁剪面)

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);

camera.position.z = 5; // 将相机向后移动5个单位



// 3. 创建渲染器

const renderer = new THREE.WebGLRenderer({ antialias: true }); // antialias开启抗锯齿

renderer.setSize(window.innerWidth, window.innerHeight); // 设置渲染尺寸

renderer.setPixelRatio(window.devicePixelRatio); // 解决高清屏模糊问题

document.body.appendChild(renderer.domElement); // 将canvas添加到页面



// 4. 创建物体：几何体 + 材质 = 网格(Mesh)

const geometry = new THREE.BoxGeometry(1, 1, 1); // 立方体几何体(宽, 高, 深)

const material = new THREE.MeshBasicMaterial({ color: 0xff0000 }); // 基础材质(红色)

const cube = new THREE.Mesh(geometry, material); // 创建网格

scene.add(cube); // 将立方体添加到场景



// 5. 添加轨道控制器（让用户可以用鼠标旋转缩放场景）

const controls = new OrbitControls(camera, renderer.domElement);

controls.enableDamping = true; // 开启阻尼效果，更平滑

controls.dampingFactor = 0.05;



// 6. 动画循环（核心）

function animate() {

&nbsp;   requestAnimationFrame(animate); // 浏览器自带的动画API，60fps

&nbsp;   

&nbsp;   // 让立方体旋转

&nbsp;   cube.rotation.x += 0.01;

&nbsp;   cube.rotation.y += 0.01;

&nbsp;   

&nbsp;   controls.update(); // 更新控制器（必须在开启阻尼时调用）

&nbsp;   renderer.render(scene, camera); // 渲染场景

}

animate();



// 7. 处理窗口大小变化

window.addEventListener('resize', () => {

&nbsp;   camera.aspect = window.innerWidth / window.innerHeight;

&nbsp;   camera.updateProjectionMatrix(); // 更新相机投影矩阵

&nbsp;   renderer.setSize(window.innerWidth, window.innerHeight);

});

```



\## 四、几何体(Geometry)详解

Three.js 提供了多种内置几何体，无需手动定义顶点：



| 几何体名称 | 构造函数 | 常用参数 |

|------------|----------|----------|

| 立方体 | BoxGeometry | width, height, depth, widthSegments, heightSegments, depthSegments |

| 球体 | SphereGeometry | radius, widthSegments, heightSegments |

| 平面 | PlaneGeometry | width, height, widthSegments, heightSegments |

| 圆柱体 | CylinderGeometry | radiusTop, radiusBottom, height, radialSegments |

| 圆锥体 | ConeGeometry | radius, height, radialSegments |

| 圆环 | TorusGeometry | radius, tube, radialSegments, tubularSegments |

| 文字 | TextGeometry | text, {font, size, height, curveSegments} |



\*\*示例：创建多种几何体\*\*

```javascript

// 球体

const sphereGeometry = new THREE.SphereGeometry(0.5, 32, 32);

const sphereMaterial = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true });

const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);

sphere.position.x = -2; // 沿X轴向左移动2个单位

scene.add(sphere);



// 圆环

const torusGeometry = new THREE.TorusGeometry(0.5, 0.2, 16, 100);

const torusMaterial = new THREE.MeshBasicMaterial({ color: 0x0000ff });

const torus = new THREE.Mesh(torusGeometry, torusMaterial);

torus.position.x = 2; // 沿X轴向右移动2个单位

scene.add(torus);

```



\## 五、材质(Material)详解

材质决定了物体的外观和对光线的反应：



\### 5.1 常用材质类型

1\. \*\*MeshBasicMaterial\*\*：基础材质，不受光照影响

2\. \*\*MeshLambertMaterial\*\*：漫反射材质，只计算漫反射，适合粗糙表面

3\. \*\*MeshPhongMaterial\*\*：高光材质，计算漫反射和高光，适合光滑表面

4\. \*\*MeshStandardMaterial\*\*：标准PBR材质，物理正确，效果最好，推荐使用

5\. \*\*MeshPhysicalMaterial\*\*：扩展的PBR材质，支持清漆、透明度等高级属性



\### 5.2 材质常用属性

```javascript

const material = new THREE.MeshStandardMaterial({

&nbsp;   color: 0xff0000, // 基础颜色

&nbsp;   map: texture, // 纹理贴图

&nbsp;   metalness: 0.5, // 金属度(0-1)，0=非金属，1=金属

&nbsp;   roughness: 0.5, // 粗糙度(0-1)，0=光滑，1=粗糙

&nbsp;   transparent: true, // 是否透明

&nbsp;   opacity: 0.8, // 透明度(0-1)

&nbsp;   wireframe: false, // 是否显示线框

&nbsp;   side: THREE.FrontSide, // 渲染面：FrontSide/BackSide/DoubleSide

});

```



\## 六、光照(Light)系统

没有光照，大部分材质都无法正确显示。Three.js 提供了多种光源：



\### 6.1 常用光源类型

```javascript

// 1. 环境光：均匀照亮所有物体，没有阴影

const ambientLight = new THREE.AmbientLight(0xffffff, 0.5); // (颜色, 强度)

scene.add(ambientLight);



// 2. 平行光：模拟太阳光，光线平行，能产生阴影

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);

directionalLight.position.set(5, 5, 5); // 设置光源位置

directionalLight.castShadow = true; // 开启阴影投射

scene.add(directionalLight);



// 3. 点光源：模拟灯泡，向四周发光，能产生阴影

const pointLight = new THREE.PointLight(0xff0000, 1, 10); // (颜色, 强度, 距离)

pointLight.position.set(0, 3, 0);

pointLight.castShadow = true;

scene.add(pointLight);



// 4. 聚光灯：模拟手电筒，锥形光束，能产生阴影

const spotLight = new THREE.SpotLight(0x00ff00, 1);

spotLight.position.set(0, 5, 0);

spotLight.angle = Math.PI / 6; // 光束角度

spotLight.castShadow = true;

scene.add(spotLight);

```



\### 6.2 开启阴影

要让物体产生和接收阴影，需要三步：

1\. 渲染器开启阴影：`renderer.shadowMap.enabled = true;`

2\. 光源开启阴影投射：`light.castShadow = true;`

3\. 物体设置：`mesh.castShadow = true;`（投射阴影）和 `mesh.receiveShadow = true;`（接收阴影）



\*\*完整阴影配置示例\*\*：

```javascript

// 渲染器配置

renderer.shadowMap.enabled = true;

renderer.shadowMap.type = THREE.PCFSoftShadowMap; // 软阴影，效果更好



// 平行光阴影优化

directionalLight.shadow.mapSize.width = 2048;

directionalLight.shadow.mapSize.height = 2048;

directionalLight.shadow.camera.near = 0.5;

directionalLight.shadow.camera.far = 50;

directionalLight.shadow.camera.left = -10;

directionalLight.shadow.camera.right = 10;

directionalLight.shadow.camera.top = 10;

directionalLight.shadow.camera.bottom = -10;



// 物体设置

cube.castShadow = true;

plane.receiveShadow = true; // 平面接收阴影

```



\## 七、纹理(Texture)使用

纹理可以让物体表面更加真实。Three.js 支持多种纹理类型：



\### 7.1 基础纹理加载

```javascript

// 创建纹理加载器

const textureLoader = new THREE.TextureLoader();



// 加载纹理

const texture = textureLoader.load(

&nbsp;   'textures/wood.jpg', // 纹理路径

&nbsp;   () => console.log('纹理加载成功'), // 成功回调

&nbsp;   undefined, // 进度回调

&nbsp;   (error) => console.error('纹理加载失败:', error) // 失败回调

);



// 应用纹理到材质

const material = new THREE.MeshStandardMaterial({

&nbsp;   map: texture, // 基础颜色纹理

&nbsp;   normalMap: normalTexture, // 法线纹理，增加表面细节

&nbsp;   roughnessMap: roughnessTexture, // 粗糙度纹理

&nbsp;   metalnessMap: metalnessTexture, // 金属度纹理

});

```



\### 7.2 纹理常用属性

```javascript

texture.wrapS = THREE.RepeatWrapping; // S方向重复

texture.wrapT = THREE.RepeatWrapping; // T方向重复

texture.repeat.set(2, 2); // 重复次数

texture.offset.set(0.5, 0.5); // 偏移

texture.rotation = Math.PI / 4; // 旋转

texture.anisotropy = renderer.capabilities.getMaxAnisotropy(); // 各向异性过滤，提高远处纹理清晰度

```



\## 八、加载外部3D模型

Three.js 最常用的模型格式是 \*\*GLTF/GLB\*\*，它是Khronos组织推出的标准3D格式，支持材质、动画、骨骼等所有特性。



\### 8.1 加载GLTF模型

```javascript

// 创建GLTF加载器

const loader = new GLTFLoader();



loader.load(

&nbsp;   'models/model.gltf', // 模型路径

&nbsp;   (gltf) => {

&nbsp;       const model = gltf.scene;

&nbsp;       model.scale.set(0.5, 0.5, 0.5); // 缩放模型

&nbsp;       model.position.y = -1; // 调整位置

&nbsp;       

&nbsp;       // 遍历模型所有子物体，开启阴影

&nbsp;       model.traverse((child) => {

&nbsp;           if (child.isMesh) {

&nbsp;               child.castShadow = true;

&nbsp;               child.receiveShadow = true;

&nbsp;           }

&nbsp;       });

&nbsp;       

&nbsp;       scene.add(model);

&nbsp;       

&nbsp;       // 如果模型有动画

&nbsp;       if (gltf.animations.length > 0) {

&nbsp;           const mixer = new THREE.AnimationMixer(model);

&nbsp;           const action = mixer.clipAction(gltf.animations\[0]);

&nbsp;           action.play();

&nbsp;           

&nbsp;           // 在动画循环中更新混合器

&nbsp;           function animate() {

&nbsp;               requestAnimationFrame(animate);

&nbsp;               const delta = clock.getDelta(); // 获取两帧之间的时间差

&nbsp;               mixer.update(delta); // 更新动画

&nbsp;               renderer.render(scene, camera);

&nbsp;           }

&nbsp;       }

&nbsp;   },

&nbsp;   (xhr) => {

&nbsp;       console.log(`加载进度: ${(xhr.loaded / xhr.total \* 100).toFixed(2)}%`);

&nbsp;   },

&nbsp;   (error) => {

&nbsp;       console.error('模型加载失败:', error);

&nbsp;   }

);

```



\## 九、动画系统

Three.js 提供了强大的动画系统，支持关键帧动画、骨骼动画等。



\### 9.1 基础动画

```javascript

// 使用Clock来计算时间差，确保动画速度一致

const clock = new THREE.Clock();



function animate() {

&nbsp;   requestAnimationFrame(animate);

&nbsp;   const elapsedTime = clock.getElapsedTime(); // 获取从开始到现在的时间

&nbsp;   

&nbsp;   // 正弦函数实现平滑的上下运动

&nbsp;   cube.position.y = Math.sin(elapsedTime) \* 2;

&nbsp;   

&nbsp;   // 匀速旋转

&nbsp;   cube.rotation.y = elapsedTime \* Math.PI;

&nbsp;   

&nbsp;   renderer.render(scene, camera);

}

```



\### 9.2 使用GSAP动画库

对于复杂的补间动画，推荐使用GSAP：

```bash

npm install gsap

```



```javascript

import gsap from 'gsap';



// 让立方体在2秒内移动到(2, 0, 0)，并旋转360度

gsap.to(cube.position, {

&nbsp;   x: 2,

&nbsp;   duration: 2,

&nbsp;   delay: 1,

&nbsp;   ease: 'power2.inOut',

&nbsp;   repeat: -1, // 无限重复

&nbsp;   yoyo: true, // 往返动画

});



gsap.to(cube.rotation, {

&nbsp;   y: Math.PI \* 2,

&nbsp;   duration: 2,

&nbsp;   delay: 1,

&nbsp;   ease: 'none',

&nbsp;   repeat: -1,

});

```



\## 十、性能优化技巧

1\. \*\*减少多边形数量\*\*：使用低多边形模型，必要时使用LOD(Level of Detail)

2\. \*\*合并几何体\*\*：使用 `BufferGeometryUtils.mergeBufferGeometries()` 合并多个几何体

3\. \*\*使用实例化渲染\*\*：对于大量相同的物体，使用 `InstancedMesh`

4\. \*\*优化纹理\*\*：压缩纹理，使用合适的分辨率，开启mipmap

5\. \*\*减少光源数量\*\*：尽量使用环境光+平行光的组合

6\. \*\*使用WebGLRenderer的优化选项\*\*：

&nbsp;  ```javascript

&nbsp;  const renderer = new THREE.WebGLRenderer({

&nbsp;      antialias: true,

&nbsp;      powerPreference: 'high-performance', // 优先使用高性能GPU

&nbsp;      alpha: false, // 如果不需要透明背景，关闭alpha

&nbsp;  });

&nbsp;  ```

7\. \*\*使用requestAnimationFrame\*\*：不要使用setInterval

8\. \*\*及时释放资源\*\*：当不再需要时，释放几何体、材质和纹理：

&nbsp;  ```javascript

&nbsp;  geometry.dispose();

&nbsp;  material.dispose();

&nbsp;  texture.dispose();

&nbsp;  ```



\## 十一、常见问题与解决方案

1\. \*\*物体不显示\*\*：

&nbsp;  - 检查相机位置是否正确，是否在物体内部

&nbsp;  - 检查物体是否添加到了场景中

&nbsp;  - 检查材质是否正确，是否有光照

&nbsp;  - 检查裁剪面设置是否正确



2\. \*\*模型加载后是黑色的\*\*：

&nbsp;  - 场景中没有添加光源

&nbsp;  - 模型的材质是MeshStandardMaterial但没有光照

&nbsp;  - 模型的法线方向反了，设置 `material.side = THREE.DoubleSide`



3\. \*\*纹理显示模糊\*\*：

&nbsp;  - 纹理分辨率太低

&nbsp;  - 没有设置 `renderer.setPixelRatio(window.devicePixelRatio)`

&nbsp;  - 开启各向异性过滤：`texture.anisotropy = renderer.capabilities.getMaxAnisotropy()`



4\. \*\*阴影有锯齿\*\*：

&nbsp;  - 增加阴影贴图分辨率：`light.shadow.mapSize.set(2048, 2048)`

&nbsp;  - 使用软阴影：`renderer.shadowMap.type = THREE.PCFSoftShadowMap`

&nbsp;  - 调整阴影相机的范围，使其刚好覆盖需要阴影的区域



\## 十二、下一步学习方向

1\. \*\*粒子系统\*\*：使用 `Points` 和 `BufferGeometry` 创建炫酷的粒子效果

2\. \*\*后期处理\*\*：使用 `EffectComposer` 添加 bloom、景深、抗锯齿等后期效果

3\. \*\*物理引擎\*\*：集成 Cannon.js 或 Ammo.js 实现真实的物理模拟

4\. \*\*VR/AR\*\*：使用 WebXR API 开发虚拟现实和增强现实应用

5\. \*\*着色器\*\*：编写自定义 GLSL 着色器，实现独特的视觉效果



\## 实战项目：3D产品展示

```javascript

import \* as THREE from 'three';

import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';



// 场景

const scene = new THREE.Scene();

scene.background = new THREE.Color(0xf0f0f0);



// 相机

const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);

camera.position.set(0, 2, 5);



// 渲染器

const renderer = new THREE.WebGLRenderer({ antialias: true });

renderer.setSize(window.innerWidth, window.innerHeight);

renderer.setPixelRatio(window.devicePixelRatio);

renderer.shadowMap.enabled = true;

renderer.shadowMap.type = THREE.PCFSoftShadowMap;

document.body.appendChild(renderer.domElement);



// 控制器

const controls = new OrbitControls(camera, renderer.domElement);

controls.enableDamping = true;

controls.dampingFactor = 0.05;

controls.minDistance = 2;

controls.maxDistance = 10;

controls.target.set(0, 1, 0);



// 光照

const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);

scene.add(ambientLight);



const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);

directionalLight.position.set(5, 5, 5);

directionalLight.castShadow = true;

directionalLight.shadow.mapSize.set(2048, 2048);

scene.add(directionalLight);



// 地面

const planeGeometry = new THREE.PlaneGeometry(10, 10);

const planeMaterial = new THREE.MeshStandardMaterial({ color: 0xcccccc });

const plane = new THREE.Mesh(planeGeometry, planeMaterial);

plane.rotation.x = -Math.PI / 2;

plane.receiveShadow = true;

scene.add(plane);



// 加载产品模型

const loader = new GLTFLoader();

loader.load('models/product.glb', (gltf) => {

&nbsp;   const product = gltf.scene;

&nbsp;   product.position.y = 1;

&nbsp;   product.traverse((child) => {

&nbsp;       if (child.isMesh) {

&nbsp;           child.castShadow = true;

&nbsp;           child.receiveShadow = true;

&nbsp;       }

&nbsp;   });

&nbsp;   scene.add(product);

});



// 动画循环

const clock = new THREE.Clock();

function animate() {

&nbsp;   requestAnimationFrame(animate);

&nbsp;   controls.update();

&nbsp;   renderer.render(scene, camera);

}

animate();



// 窗口大小变化

window.addEventListener('resize', () => {

&nbsp;   camera.aspect = window.innerWidth / window.innerHeight;

&nbsp;   camera.updateProjectionMatrix();

&nbsp;   renderer.setSize(window.innerWidth, window.innerHeight);

});

```



\## 学习资源推荐

\- \*\*官方文档\*\*：https://threejs.org/docs/

\- \*\*官方示例\*\*：https://threejs.org/examples/

\- \*\*Three.js Journey\*\*：https://threejs-journey.com/（最全面的Three.js教程）

\- \*\*Bruno Simon作品集\*\*：https://bruno-simon.com/（Three.js大神作品）



