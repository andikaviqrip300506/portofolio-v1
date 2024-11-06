<template>
  <header class="w-full">
    <nav class="lg:mx-7">
      <ul class="flex justify-between items-center">
        <li><span class="ml-7 text-xl"><\> </span></li>
        <li>
          <div ref="lottieContainer" class="w-28 h-28"></div>
        </li>
      </ul>
    </nav>
  </header>
  <main class="flex flex-col gap-36 mt-11">
    <section class="relative">
      <div class="flex flex-col text-center gap-5">
        <h1>
          <span class="text-7xl lg:text-8xl font-extrabold">PRATAMA</span>
        </h1>
        <h1>
          <span class="text-7xl lg:text-8xl font-extrabold text-andika"
            >ANDIKA</span
          >
        </h1>
        <h1><span class="text-7xl lg:text-8xl font-extrabold">VIQRI</span></h1>
      </div>
      <div
        class="absolute w-36 h-36 lg:w-48 lg:h-48 bg-slate-100 rounded-full top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 opacity-1"
      >
        <img
          src="../assets/image/foto.jpg"
          class="w-full h-full rounded-md object-cover grayscale hover:grayscale-0"
          alt=""
        />
      </div>
    </section>

    <section class="flex mt-36 lg:p-20 p-10" ref="section">
      <div class="lg:basis-2/5">
        <div ref="airplaneContainer" class="threejs-airplane"></div>
      </div>
      <div id="text" class="text-container lg:basis-3/5">
        <p class="text-xl leading-10">
          <span
            v-for="(word, index) in words"
            :key="index"
            class="word"
            ref="words"
          >
            {{ word }}
          </span>
        </p>
      </div>
    </section>
    <p>hai</p>
    <p>hai</p>
    <p>hai</p>
    <p>hai</p>
    <p>hai</p>
    <p>hai</p>
  </main>
  <footer class=""></footer>
</template>

<script>
import lottie from "lottie-web";
import animationData from "../assets/image/language.json";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import * as THREE from "three";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { nextTick } from "vue";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

gsap.registerPlugin(ScrollTrigger);

export default {
  data() {
    return {
      words: [],
      isScrolling: false,
    };
  },
  async mounted() {
    lottie.loadAnimation({
      container: this.$refs.lottieContainer,
      renderer: "svg",
      loop: true,
      autoplay: true,
      animationData: animationData,
      rendererSettings: {
        preserveAspectRatio: "xMidYMid slice",
      },
    });

    const text =
      "Hi there 👋, I’m Andika Viqri Pratama, I’m currently living in Bandung, Indonesia. I bring over 1 year of experience as a Front End Web Developer. I’m skilled in creating, developing, and managing websites, specializing in Vue.js and Nuxt.js. I’m not just a coder but also a problem solver, always eager to explore new technologies.";
    this.words = text.split(" ");

    await nextTick();

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      this.$refs.airplaneContainer.offsetWidth /
        this.$refs.airplaneContainer.offsetHeight,
      0.1,
      1000
    );
    camera.position.z = 5;

    const renderer = new THREE.WebGLRenderer({ alpha: true });
    renderer.setSize(
      this.$refs.airplaneContainer.offsetWidth,
      this.$refs.airplaneContainer.offsetHeight
    );
    this.$refs.airplaneContainer.appendChild(renderer.domElement);

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;

    const loader = new GLTFLoader();
    loader.load("/scene.gltf", (gltf) => {
      const model = gltf.scene;
      model.traverse((node) => {
        if (node.isMesh) {
          node.material.color.set(0xff0000); // Mengubah warna menjadi merah
        }
      });
      model.scale.set(2, 2, 2);
      scene.add(model);

      const animate = () => {
        requestAnimationFrame(animate);
        controls.update();
        renderer.render(scene, camera);
      };
      animate();

      gsap.to(model.position, {
        x: 2,
        y: -2,
        z: -3,
        scrollTrigger: {
          trigger: this.$refs.section,
          start: "top center",
          end: "bottom center",
          scrub: 1,
        },
        onUpdate: () => {
          model.rotation.x += 0.15; 
          model.rotation.z += 0.05;
        },
      });
    });

    gsap.fromTo(
      this.$refs.words,
      { opacity: 0.1 },
      {
        opacity: 1,
        stagger: 0.1,
        duration: 0.5,
        scrollTrigger: {
          trigger: this.$refs.section,
          start: "top center",
          end: "bottom center",
          toggleActions: "play none none reverse",
          scrub: true,
        },
      }
    );

    let scrollTimeOut;
    window.addEventListener("scroll", () => {
      clearTimeout(scrollTimeOut);

      if (!this.isScrolling) {
        this.isScrolling = true;
      }

      scrollTimeOut = setTimeout(() => {
        this.isScrolling = false;
        tween.pause();
        airplaneTween.pause();
      }, 100);

      if (this.isScrolling) {
        tween.play();
        airplaneTween.play();
      }
    });
  },
};
</script>

<style scoped>
.text-container {
  transition: opacity 0.5s ease-in-out;
  word-spacing: 0.5rem;
}
.word {
  display: inline-block;
  opacity: 0;
  margin-right: 0.25rem;
}

.paper-airplane {
  position: absolute;
  width: 100px;
  height: 100px;
  background: lightgray;
  transform-origin: center;
  clip-path: polygon(0 0, 100% 50%, 0 100%);
  transform: perspective(1000px) rotateX(15deg) rotateY(30deg);
  transition: transform 0.3s ease, opacity 0.3s ease;
}

.threejs-airplane {
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
