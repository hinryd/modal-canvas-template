<template>
  <div class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
          <slot name="header">
            <button @click="clearInk">clear canvas</button>
          </slot>
        </div>

        <div class="modal-body">
          <canvas
            id="drawingCanvas"
            v-on="{
              mousedown: startInk,
              mouseup: endInk,
              mousemove: drawInk,
            }"
          />
        </div>

        <div class="modal-footer">
          <slot name="footer">
            <button @click="saveInk">save canvas</button>
            <button class="modal-default-button" @click="$emit('close')">
              OK
            </button>
          </slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Modal",
  mounted() {
    const canvas = document.getElementById("drawingCanvas");
    const canvasRect = canvas.getBoundingClientRect();
    const ctx = canvas.getContext("2d");

    canvas.width = canvasRect.width;
    canvas.height = canvasRect.height;

    ctx.lineCap = "round";
    ctx.strokeStyle = "black";
    ctx.lineWidth = 5;

    this.canvas = canvas;
    this.canvasRect = canvasRect;
    this.canvasCtx = ctx;
  },
  data() {
    return {
      canvas: null,
      canvasRect: null,
      canvasCtx: null,
      isDrawing: false,
      signature: null,
    };
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
      console.log(this.canvas.toDataURL("image/png"));
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

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 1500px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
