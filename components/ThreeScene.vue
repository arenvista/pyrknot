<script setup>
import { ref, onMounted, onBeforeUnmount, watch } from 'vue'
import * as THREE from 'three'

const props = defineProps({
  type: {
    type: String,
    default: 'one' // 'one', 'slide1', 'two', 'full', 'slide5'
  }
})

const canvasRef = ref(null)
const containerRef = ref(null)
let reqId = null
let renderer = null
let resizeObserver = null

// --- Constants ---
const AXES = [
  new THREE.Vector3(0, 1, 0),
  new THREE.Vector3(1, 0, 0),
  new THREE.Vector3(0.577, 0.577, 0.577).normalize(),
  new THREE.Vector3(-0.577, 0.577, 0.577).normalize(),
  new THREE.Vector3(0.577, -0.577, 0.577).normalize(),
  new THREE.Vector3(0.2, 0.8, 0.5).normalize(),
  new THREE.Vector3(0.7, 0.1, -0.7).normalize()
];
const COL_CIRCLE = [0x3B8BD4, 0xD85A30, 0x639922, 0xD4537E, 0xBA7517, 0x533AB7, 0xE24B4A];

// --- Reuseable Objects for Animation (Performance) ---
const _tempVec = new THREE.Vector3();
const _tempQuat = new THREE.Quaternion();
const UP = new THREE.Vector3(0, 1, 0);

const createGreatCircle = (axis, color) => {
  const segments = 128;
  const pts = [];
  const q = new THREE.Quaternion().setFromUnitVectors(UP, axis.clone().normalize());
  
  for (let i = 0; i <= segments; i++) {
    const a = (i / segments) * Math.PI * 2;
    const v = new THREE.Vector3(Math.cos(a) * 1.01, 0, Math.sin(a) * 1.01).applyQuaternion(q);
    pts.push(v);
  }
  
  const geo = new THREE.BufferGeometry().setFromPoints(pts);
  const mat = new THREE.LineBasicMaterial({ color, transparent: true, opacity: 0.6 });
  return new THREE.Line(geo, mat);
}

onMounted(() => {
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(42, 1, 0.1, 100);
  
  renderer = new THREE.WebGLRenderer({ 
    canvas: canvasRef.value, 
    alpha: true, 
    antialias: true,
    powerPreference: "high-performance" 
  });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // --- Lighting ---
  scene.add(new THREE.AmbientLight(0xffffff, 0.8));
  const dirLight = new THREE.DirectionalLight(0xffffff, 1.0);
  dirLight.position.set(3, 5, 4);
  scene.add(dirLight);

  // --- Central Sphere ---
  const sphereMat = new THREE.MeshStandardMaterial({ 
    roughness: 0.7, 
    transparent: true, 
    opacity: 0.85
  });
  
  const updateSphereColor = () => {
    const isDark = document.documentElement.classList.contains('dark');
    sphereMat.color.set(isDark ? 0x374151 : 0xd1d5db);
  };
  updateSphereColor();
  scene.add(new THREE.Mesh(new THREE.SphereGeometry(1, 64, 64), sphereMat));

  // --- Watch for Theme Changes ---
  const observer = new MutationObserver(updateSphereColor);
  observer.observe(document.documentElement, { attributes: true, attributeFilter: ['class'] });

  // --- Unified Geometry & Orbiting Bodies Logic ---
  const orbitingBodies = [];
  const orbitRadius = 1.6;

  // Determine number of orbs based on the prop type
  let numOrbs = 0;
  if (props.type === 'full' || props.type === 'slide5') {
    numOrbs = 7;
  } else if (props.type === 'two') {
    numOrbs = 2;
  } else if (props.type === 'slide1') {
    numOrbs = 1;
  } // 'one' defaults to 0 elements

  // Dynamically generate the paths and their corresponding orbs
  for (let i = 0; i < numOrbs; i++) {
    let axis = AXES[i];
    const color = COL_CIRCLE[i % COL_CIRCLE.length];

    // Axis modifiers to match previous layout behaviors
    if (props.type === 'two') {
      axis = i === 0 ? AXES[1] : AXES[0];
    } else if (props.type === 'slide5') {
      axis = AXES[(i + 1) % AXES.length]; // Modulo added to prevent out-of-bounds error
    }

    // 1. Add orbit path (Great Circle)
    scene.add(createGreatCircle(axis, color));

    // 2. Add Orb mesh
    const bodyMesh = new THREE.Mesh(
      new THREE.SphereGeometry(0.06, 16, 16),
      new THREE.MeshStandardMaterial({ 
        color: color, 
        emissive: color, 
        emissiveIntensity: 0.4 
      })
    );
    scene.add(bodyMesh);
    orbitingBodies.push({ mesh: bodyMesh, axis: axis });
  }

  // --- Responsive Resize ---
  resizeObserver = new ResizeObserver(([entry]) => {
    const { width, height } = entry.contentRect;
    camera.aspect = width / height;
    camera.updateProjectionMatrix();
    renderer.setSize(width, height, false);
  });
  resizeObserver.observe(containerRef.value);

  // --- Animation Loop ---
  let t = 0;
  const animate = () => {
    t += 0.005;

    orbitingBodies.forEach((body) => {
      _tempQuat.setFromUnitVectors(UP, body.axis);
      const orbitTime = t * 0.5;
      
      _tempVec.set(
        Math.cos(orbitTime) * orbitRadius, 
        0, 
        Math.sin(orbitTime) * orbitRadius
      ).applyQuaternion(_tempQuat);

      body.mesh.position.copy(_tempVec);
    });

    camera.position.set(Math.sin(t) * 5.5, 1.2, Math.cos(t) * 5.5);
    camera.lookAt(0, 0, 0);
    
    renderer.render(scene, camera);
    reqId = requestAnimationFrame(animate);
  }
  animate();

  onBeforeUnmount(() => {
    observer.disconnect();
    if (reqId) cancelAnimationFrame(reqId);
    if (renderer) renderer.dispose();
    if (resizeObserver) resizeObserver.disconnect();
  });
});
</script>

<template>
  <div ref="containerRef" class="w-full h-[350px] mt-6 flex justify-center overflow-hidden">
    <canvas ref="canvasRef" class="w-full h-full outline-none block"></canvas>
  </div>
</template>
