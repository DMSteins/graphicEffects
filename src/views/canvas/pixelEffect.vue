<script setup>
import { ref, onMounted, onUnmounted } from "vue"

const canvasRef = ref(null);
let pointsData = [];
/**@type {CanvasRenderingContext2D} */
let canvas2d = null;
let textMeta = {}
let w = 1800, h = 900, pixRatio = 3
const movePoints = (e) => {
    console.log("enter")
    if(!pointsData.length || e.offsetX < 100 || e.offsetY < 100) return
    for (let i = 0; i < pointsData.length; i++){
        if (pointsData[i].ex % 6 < 3 || pointsData[i].ey % 6 < 3) {
            pointsData[i].hide = true
        }
        // const x = pointsData[i].ex
        // const y = pointsData[i].ey
        // if (x < e.offsetX + 20 && x > e.offsetX - 20
        // && y < e.offsetY + 20 && y > e.offsetY - 20) {
        //     pointsData[i].x = x + (x - e.offsetX) * Math.random() * 10 >> 0
        //     pointsData[i].y = y + (y - e.offsetY) * Math.random() * 10 >> 0
        // }
    }
    requestAnimationFrame(drawCanvas)
}
const mouseleave = (e) => {
    console.log("leave")
    requestAnimationFrame(resetPoints)
}
const resetPoints = () => {
    canvas2d.clearRect(0, 0, w, h)
    for (let i = 0; i < pointsData.length; i++) {
        const x = pointsData[i].ex
        const y = pointsData[i].ey
        const color = `rgba(${pointsData[i].r},${pointsData[i].g},${pointsData[i].b},${pointsData[i].a})`
        canvas2d.fillStyle = color
        canvas2d.fillRect(x, y, 8, 8)
    }
}
const drawCanvas = () => {
    canvas2d.clearRect(0, 0, w, h)
    for (let i = 0; i < pointsData.length; i++) {
        if(pointsData[i].hide) continue
        const x = pointsData[i].ex
        const y = pointsData[i].ey
        const color = `rgba(${pointsData[i].r},${pointsData[i].g},${pointsData[i].b},${pointsData[i].a})`
        canvas2d.fillStyle = color
        canvas2d.fillRect(x, y, 8, 8)
    }
    // requestAnimationFrame(drawCanvas)
}
onMounted(() => {
    /**@type {HTMLCanvasElement} */
    const canvas = canvasRef.value
    const context = canvas.getContext("2d")
    canvas2d = context
    context.fillStyle = "orange";
    context.font = "bold 300px serif"
    context.textAlign = "center"
    context.textBaseline = "middle"
    context.fillText("世界", w / 2, h / 2)
    const pixelData = context.getImageData(0, 0, w, h)
    context.clearRect(0, 0, w, h)
    const textPixs = []
    let minx = 600, miny = 600
    let maxx = 0, maxy = 0
    for (let j = 0; j < pixelData.height; j+=12){
        for (let i = 0; i < pixelData.width; i+=12){
            // 每个点4个rgba值，一行有pw个点，j行有 j * pw个点
            // 当前像素点起始位置
            const pix = (j * pixelData.width + i) * 4
            // 该点的rgb有值
            if (pixelData.data[pix] + pixelData.data[pix + 1] + pixelData.data[pix + 2] > 0) {
                textPixs.push({
                    cx: i,
                    cy: j,
                    ex: i,
                    ey: j,
                    r: pixelData.data[pix],
                    g: pixelData.data[pix + 1],
                    b: pixelData.data[pix + 2],
                    a: pixelData.data[pix + 3],
                })
                minx = Math.min(minx, i)
                miny = Math.min(miny, j)
                maxx = Math.max(maxx, i)
                maxy = Math.max(maxy, j)
            }
        }
    }
    console.log(minx, miny, maxx, maxy, textPixs.length)
    pointsData = textPixs
    resetPoints()
})
</script>
<template>
    <div>
        <div>
            <canvas @mouseenter="movePoints" @mouseleave="mouseleave" ref="canvasRef" :width="w" :height="h" class="canvas"></canvas>
        </div>
    </div>
</template>
<style scoped>
.canvas{
    height: 300px;
    width:600px;
}
</style>