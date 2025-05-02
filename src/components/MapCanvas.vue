<template>
  <div class="w-full h-full">
    <canvas ref="canvas" class="border border-gray-400 w-full h-full" />
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'

const canvas = ref(null)

// Mock map data from backend
const mapWidth = 400
const mapHeight = 400
const resolution = 0.05 // 5cm per cell

// Simulated map grid (black = 100, free = 0, unknown = -1)
const mapData = Array.from({ length: mapWidth * mapHeight }, () => {
  const r = Math.random()
  return r < 0.15 ? 100 : r < 0.25 ? -1 : 0
})

// Robots with x/y/theta in meters
const robots = [{ id: 'robot-1', x: 2.5, y: 3.5, theta: 0 }]

const scale = 6 // pixels per map cell

function drawMap(ctx) {
  for (let y = 0; y < mapHeight; y++) {
    for (let x = 0; x < mapWidth; x++) {
      const idx = y * mapWidth + x
      const val = mapData[idx]
      if (val === 100) ctx.fillStyle = 'black'
      else if (val === 0) ctx.fillStyle = '#606060'
      else ctx.fillStyle = 'black' // unknown

      ctx.fillRect(x, y, 1, 1)
    }
  }
}

function drawRobots(ctx) {
  robots.forEach((robot) => {
    const px = robot.x / resolution
    const py = robot.y / resolution
    const r = 0.2 / resolution

    ctx.strokeStyle = '#00bfff'
    ctx.lineWidth = 0.1 / resolution
    ctx.beginPath()
    ctx.arc(px, py, r + 3, 0, Math.PI * 2)
    ctx.stroke()

    ctx.fillStyle = 'black'
    ctx.beginPath()
    ctx.arc(px, py, r, 0, Math.PI * 2)
    ctx.fill()

    ctx.strokeStyle = 'red'
    ctx.beginPath()
    ctx.moveTo(px, py)
    ctx.lineTo(px + r * 1.5 * Math.cos(robot.theta), py + r * 1.5 * Math.sin(robot.theta))
    ctx.stroke()
  })
}

onMounted(() => {
  const c = canvas.value
  const ctx = c.getContext('2d')

  c.width = mapWidth * scale
  c.height = mapHeight * scale

  ctx.save()
  ctx.scale(scale, scale)
  drawMap(ctx)
  drawRobots(ctx)
  ctx.restore()
})
</script>
