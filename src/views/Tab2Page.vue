<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Tab 2</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab Skecth</ion-title>
        </ion-toolbar>
      </ion-header>

      <Editor :canvasWidth="canvasWidth" :canvasHeight="canvasHeight" ref="editor" editorId="canvasId" />
      <canvas ref="burnAreaCanvas"></canvas>

    </ion-content>
  </ion-page>
</template>

<script  lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent } from '@ionic/vue';
import { defineComponent } from 'vue';
// import Editor from 'vue-image-markup';

export default defineComponent({
  components: { IonPage, IonHeader, IonToolbar, IonTitle, IonContent },
  setup() {

    const canvasWidth = 500;
    const canvasHeight = 500;
    return {
      canvasHeight, canvasWidth
    }
  },
  data() {
    return {
      isDrawing: false,
      totalBurnArea: 0,
    }
  },
  computed: {
    canvas() {
      return this.$refs.burnAreaCanvas as any
    },
    context() {
      return this.canvas.getContext('2d')
    },
  },
  mounted() {
    this.setupCanvas()
  },
  methods: {
    setupCanvas() {
      this.canvas = this.$refs.burnAreaCanvas
      this.context = this.canvas.getContext('2d')
      // set the canvas background to transparent
      this.context.clearRect(0, 0, this.canvas.width, this.canvas.height)
      // create a new image object
      const image = new Image();
      image.src = '/assets/images/Man.png';
      image.onload = () => {
        this.context.drawImage(image, 0, 0);
        // add event listeners
        this.canvas.addEventListener('mousedown', this.startDrawing)
        this.canvas.addEventListener('mousemove', this.draw)
        this.canvas.addEventListener('mouseup', this.stopDrawing)
      }
    },
    startDrawing(event: { clientX: any; clientY: any; }) {
      this.isDrawing = true
      this.context.beginPath()
      this.context.moveTo(event.clientX, event.clientY)
    },
    draw(event: { clientX: any; clientY: any; }) {
      if (!this.isDrawing) return
      // check if the pixel under the cursor is transparent
      const x = event.clientX;
      const y = event.clientY;
      const pixel = this.context.getImageData(x, y, 1, 1);
      if (pixel.data[3] === 0) {
        this.context.lineTo(x, y)
        this.context.stroke()
      }
    },
    stopDrawing() {
      this.isDrawing = false
      this.context.closePath()

      // calculate burn area
      const imageData = this.context.getImageData(0, 0, this.canvas.width, this.canvas.height)
      for (let i = 0; i < imageData.data.length; i += 4) {
        if (imageData.data[i] === 0) {
          this.totalBurnArea++
        }
      }
    }
  }
})

</script>
