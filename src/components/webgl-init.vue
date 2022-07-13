<template>
  <div id="canvas">
    <div id="full" @click="fullClick"><button>全屏</button></div>
  </div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
// 动画库
import gsap from 'gsap';
// dat.gui.js是一个一个轻量级的图形用户界面库，或者说GUI组件，只有几十KB，可以用于创建操作控制三维场景的菜单栏，比如渲染的时候通过鼠标调试光照参数， 要比手动更改参数再刷新浏览器要快捷方便得多。
import * as dat from 'dat.gui';

export default {
  name: 'webGl',
  data(){
    return{
      scene: null,
      camera: null,
      renderer: null,
      cube: null
    }
  },
  async mounted(){
    await this.init();
    await this.initDat();

  },
  methods:{

    // 图形化界面库
    initDat(){
      const gui = new dat.GUI();
      gui.add(this.cube.position, 'x')
      .min(0)
      .max(5)
      .step(0.01)
      .name('移动x轴')
      .onChange(value=>{
        console.log('运动值改变',value)
      })
      .onFinishChange(value=>{
        console.log('运动停止',value)
      })
      //创建控件对象变量
      let controls={
          scale: 1,
          rotation: 0.01,
          color : '#f40',
          fn:()=>{
            gsap.to(this.cube.scale,{
              duration:1,
              x: 2,
              yoyo: true
            })
          }
       };
      gui.addColor(controls, 'color')
      .onChange(value=>{
        this.cube.material.color.set(value);
      }); //添加颜色菜单选项

      gui.add(controls, 'rotation',0.1,2.5).name('旋转');//旋转
      gui.add(this.cube, 'visible') //显示隐藏
      gui.add(controls, 'fn') //显示隐藏

      // 创建文件夹
      let folder = gui.addFolder('文件夹');
      folder.add(controls,'scale',1,4);
      folder.add(this.cube.material, 'wireframe');
    },

    // 全屏切换
    fullClick() {
      let isFull = document.fullscreenElement;
      if(!isFull){
        // 全屏
        this.renderer.domElement.requestFullscreen();
      }else{
        // 退出全屏
        document.exitFullscreen();
      }
    },

    init() {
      // 1. 创建场景
      const scene = new THREE.Scene();
      // 2. 创建一个镜头
      const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );
      // 设置相机的位置
      camera.position.set(0,0,10);
      // 3. 相机添加到场景中
      scene.add(camera);

      // 4. 创建以一个几何体
      const geometry = new THREE.BoxGeometry( 1, 1, 1 );
      // 5. 几何体的物料
      const material = new THREE.MeshBasicMaterial( {color: 0xffff00} );

      // 6. 根据几何体和材质创建物体
      const cube = new THREE.Mesh( geometry, material );

      console.log(geometry)

      // 当前几何体对象
      console.log(cube)

      

      // 位置
      // 可以设置几何对象的位置，默认值是（0,0,0)在页面的正中央
      // 可以通过set(x,y,z)
      // cube.position.set(5,0,0);
      // 也可以通过.的方式
      // cube.position.x = 5;

      // 放大
      // 可以设置几何对象的大小，默认值是（0,0,0)
      // cube.scale.set(2,1,1);
      // cube.scale.x = 2

      // 旋转, 第四个参数可以更改渲染顺序
      //  默认值围绕(x,y,x,'xyz')
      // cube.rotation.set(45,0,0)
      // cube.rotation.x = 45;


      // 7. 几何体添加到场景中
      scene.add(cube);
      
      // 8. 创建webgl
      const renderer = new THREE.WebGLRenderer();
      // 设置webgl的大小
      renderer.setSize(window.innerWidth, window.innerHeight); // 渲染大小
      let canvas = document.getElementById('canvas')
      // 将webgl添加到dom上
      canvas.appendChild(renderer.domElement);

        // 使用渲染器将镜头和场景结合
        // renderer.render(scene,camera)

        // 9.创建轨道控制器, 用来查看物体
        const controls = new OrbitControls(camera,renderer.domElement);
        console.log(controls);
        // 10. 创建坐标轴x，y,z
        // 参数是线的长度
        // 红色代表 X 轴. 绿色代表 Y 轴. 蓝色代表 Z 轴.
        const axesHelper = new THREE.AxesHelper(5)
        //  添加到场景中
        scene.add(axesHelper);


        // 11. 通过Gsap来创建动画 
        // gsap.to(目标，{duration: 时长，x: 位移的值, ease： 运动轨迹})
        // repeat: 重复次数，当-1时一直重复
        // yoyo: 如果为true，则每次重复播放都会前后交替进行。
        let animation = gsap.to(
          cube.position,
          { 
            duration: 5, 
            x:5, 
            ease: "circ.out",
            repeat: 3,
            yoyo: true,
            onComplete(){
              console.log('动画完成后的回调')
            },
            onStart(){
              console.log('动画完开始时的回调')
            }
          }
        )
        gsap.to(
          cube.rotation,
          { 
            duration: 5, 
            x: 2 * Math.PI, 
            ease: "circ.out"
          }
        )

        document.body.addEventListener('dblclick',()=>{
          // isActive() 是否正在运动
          if(animation.isActive()){
            // 暂停
            animation.pause()
          }else{
            // 恢复
            animation.resume()
          }
        })


        // 当旋转轨道时，进行渲染更新
        // eslint-disable-next-line no-unused-vars
        function render(time) {
          // time 默认参数，每隔多少秒执行依次，时间间隔是毫秒
          // console.log(time)

          // 设置cube的位置
          // cube.position.x+=0.01;
          // if(cube.position.x > 5) cube.position.x = 0;
          
          // 自己设置简单的动画
          // let t = ( time / 1000 ) % 5;
          // cube.position.x = t * 1;
          // if(cube.position.x > 5) cube.position.x = 0;
          // // 设置旋转
          // cube.rotation.x = t * 5;

          // 每一帧进行渲染，会进行调用
          requestAnimationFrame( render );

          // controls.update();

          renderer.render( scene, camera );

        }
        render();


        // 12. 尺寸变化，自适应画面
        window.addEventListener('resize',()=>{
          // 更新摄像头
          camera.aspect = window.innerWidth / window.innerHeight;
          // 更新摄像机的投影矩阵
          camera.updateProjectionMatrix();

          // 更新渲染器
          renderer.setSize(window.innerWidth,window.innerHeight);
          // 更新渲染器的像素比
          // 关于像素比，pc端是1，移动端是2
          // 移动端适配，没起作用？？？
          renderer.setPixelRatio(window.devicePixelRatio)
        })



        this.renderer = renderer;
        this.cube = cube;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#full{
  position: absolute;
  bottom: 0;
  right: 0;
}
</style>
