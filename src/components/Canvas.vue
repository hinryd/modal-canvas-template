<template>
  <div>
    <button @click="clearInk">clear canvas</button>
    <canvas
      id="drawingCanvas"
      v-on="{
        mousedown: startInk,
        mouseup: endInk,
        mousemove: drawInk,
      }"
    />
    <button @click="saveInk">save canvas</button>
  </div>
</template>

<script>
export default {
  name: "Canvas",
  data() {
    return {
      canvas: null,
      canvasRect: null,
      canvasCtx: null,
      isDrawing: false,
      signature: null,
    };
  },
  mounted() {
    const canvas = document.getElementById("drawingCanvas");
    const canvasRect = canvas.getBoundingClientRect();
    const ctx = canvas.getContext("2d");

    ctx.canvas.width = canvasRect.width;
    ctx.canvas.height = canvasRect.height;
    ctx.lineCap = "round";
    ctx.strokeStyle = "black";
    ctx.lineWidth = 5;

    this.canvasRect = canvasRect;
    this.canvasCtx = ctx;
  },
  methods: {
    startInk(e) {
      const { x, y } = e;
      const [actualX, actualY] = this.getCursorPosition(x, y);
      this.canvasCtx.beginPath();
      this.canvasCtx.moveTo(actualX, actualY);
      this.isDrawing = true;
    },
    drawInk(e) {
      if (!this.isDrawing) return;
      const { x, y } = e;
      const [actualX, actualY] = this.getCursorPosition(x, y);
      this.canvasCtx.lineTo(actualX, actualY);
      this.canvasCtx.stroke();
    },
    endInk() {
      this.canvasCtx.closePath();
      this.isDrawing = false;
    },
    clearInk() {
      this.canvasCtx.clearRect(
        0,
        0,
        this.canvasRect.width,
        this.canvasRect.height
      );
    },
    saveInk() {
      const ctx = this.canvasCtx;
      const cropImageFromCanvas = (ctx) => {
        var canvas = ctx.canvas,
          w = canvas.width,
          h = canvas.height,
          pix = { x: [], y: [] },
          imageData = ctx.getImageData(0, 0, canvas.width, canvas.height),
          x,
          y,
          index;

        for (y = 0; y < h; y++) {
          for (x = 0; x < w; x++) {
            index = (y * w + x) * 4;
            if (imageData.data[index + 3] > 0) {
              pix.x.push(x);
              pix.y.push(y);
            }
          }
        }
        pix.x.sort(function(a, b) {
          return a - b;
        });
        pix.y.sort(function(a, b) {
          return a - b;
        });
        var n = pix.x.length - 1;

        w = 1 + pix.x[n] - pix.x[0];
        h = 1 + pix.y[n] - pix.y[0];
        var cut = ctx.getImageData(pix.x[0], pix.y[0], w, h);

        canvas.width = w;
        canvas.height = h;
        ctx.putImageData(cut, 0, 0);

        var image = canvas.toDataURL(); //open cropped image in a new window
        var win = window.open(image, "_blank");
        win.focus();
      };
      cropImageFromCanvas(ctx);
    },
    getCursorPosition(x, y) {
      return [x - this.canvasRect.left, y - this.canvasRect.top];
    },
  },
};
</script>

<style scoped>
#drawingCanvas {
  width: 100%;
  height: 100%;
  border-width: 2px;
  border-color: black;
  border-style: solid;
}
</style>
