<script setup>
import { ref, onMounted, onUnmounted } from "vue"

const canvasRef = ref(null);
let pointsData = [];
/**@type {CanvasRenderingContext2D} */
let canvas2d = null;
const movePoints = (e) => {
    if(!pointsData.length || e.offsetX < 100 || e.offsetY < 100) return
    for (let i = 0; i < pointsData.length; i++){
        const x = pointsData[i].ex
        const y = pointsData[i].ey
        if (x < e.offsetX + 20 && x > e.offsetX - 20
        && y < e.offsetY + 20 && y > e.offsetY - 20) {
            pointsData[i].x = x + (x - e.offsetX) * Math.random() * 10 >> 0
            pointsData[i].y = y + (y - e.offsetY) * Math.random() * 10 >> 0
        }
    }
    requestAnimationFrame(drawCanvas)
}
const drawCanvas = () => {
    canvas2d.clearRect(0, 0, 600, 300)
    for (let i = 0; i < pointsData.length; i++) {
        const x = pointsData[i].x
        const y = pointsData[i].y
        const color = `rgba(${pointsData[i].r},${pointsData[i].g},${pointsData[i].b},1)`
        canvas2d.fillStyle = color
        canvas2d.fillRect(x, y, 1, 1)
    }
    // requestAnimationFrame(drawCanvas)
}
onMounted(() => {
    /**@type {HTMLCanvasElement} */
    const canvas = canvasRef.value
    const context = canvas.getContext("2d")
    canvas2d = context
    context.fillStyle = "orange";
    context.font = "bold 100px serif"
    context.fillText("像素点", 100, 100)
    const pixelData = context.getImageData(0, 0, 600, 300)
    const textPixs = []
    for (let j = 0; j < pixelData.height; j++){
        for (let i = 0; i < pixelData.width; i++){
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
            }
        }
    }
    pointsData = textPixs
})
onMounted(() => {
    canvasRef.value && canvasRef.value.addEventListener("mousemove", movePoints)
})
onUnmounted(() => {

    canvasRef.value && canvasRef.value.removeEventListener("mousemove", movePoints)
})
</script>
<template>
    <div>
        111
        <div>
            <canvas ref="canvasRef" width="600" height="300" class="canvas"></canvas>
        </div>
    </div>
</template>
<style scoped>
.canvas{
    height: 300px;
    width:600px;
}
</style>