<script setup>
const cx = 110, cy = 105, rx = 90, ry = 32, off = 12;
const dots = Array.from({ length: 12 }).map((_, i) => {
  const a = (i / 12) * Math.PI * 2;
  return {
    x: cx + rx * Math.cos(a),
    y: cy + ry * Math.sin(a),
    tx: cx + (rx + off) * Math.cos(a),
    ty: cy + (ry + off) * Math.sin(a),
    num: i + 1
  };
});
</script>

<template>
  <svg viewBox="0 0 620 210" class="w-full max-w-[650px] h-auto mx-auto block">
    <ellipse :cx="cx" :cy="cy" :rx="rx" :ry="rx" fill="none" stroke="currentColor" stroke-opacity="0.3" stroke-width="1"/>
    <ellipse :cx="cx" :cy="cy" :rx="rx" :ry="ry" fill="none" stroke="#3B8BD4" stroke-width="2.5"/>
    <g v-for="d in dots" :key="d.num">
      <circle :cx="d.x" :cy="d.y" r="4" fill="#639922" />
      <text :x="d.tx" :y="d.ty" text-anchor="middle" dominant-baseline="central" font-size="5" font-weight="600" fill="currentColor" opacity="0.7">{{ d.num }}</text>
    </g>
    <line x1="218" y1="105" x2="290" y2="105" stroke="currentColor" stroke-opacity="0.3" stroke-width="1"/>
    <rect x="305" y="36" width="218" height="143" rx="10" fill="currentColor" fill-opacity="0.05" stroke="currentColor" stroke-opacity="0.2"/>
    <text x="315" y="55" font-size="5"fill="currentColor" font-weight="600">Edges per circle:</text>
    <text x="315" y="77" font-size="5"fill="currentColor" opacity="0.7">6 others × 2 = 12 pts</text>
    <text x="315" y="99" font-size="5"fill="currentColor" opacity="0.7">12 pts → 12 segments</text>
    <text x="315" y="125" font-size="5"fill="currentColor" font-weight="600">Total edges E:</text>
    <text x="315" y="147" font-size="5"fill="currentColor" opacity="0.7">7 circles × 12 = 84</text>
  </svg>
</template>
