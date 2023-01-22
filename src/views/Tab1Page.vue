<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Camera Sample</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Tab 1</ion-title>
        </ion-toolbar>
      </ion-header>


      <div id="camera" class="camera" v-if="!imageCapture">
        <div class="burn-area">
          <img :src="require(`@/assets/images/${bodyType}.png`)" />
        </div>
      </div>

      <div class="result" v-else>
        <img :src="imageCapture" />
        <ion-button @click="imageCapture = null; startCamera()">Back</ion-button>
      </div>

    </ion-content>

    <ion-footer>
      <ion-toolbar>
        <ion-segment @ionChange="segmentChanged($event)" value="Man">
          <ion-segment-button value="Man">
            <ion-label>Lanang</ion-label>
          </ion-segment-button>
          <ion-segment-button value="Woman">
            <ion-label>Banci</ion-label>
          </ion-segment-button>
        </ion-segment>
      </ion-toolbar>


      <ion-toolbar class="ion-text-center">
        <ion-button v-if="isOn" @click="stopCamera()">
          <ion-icon :icon="stop"></ion-icon>
        </ion-button>

        <ion-button v-else @click="startCamera()">
          <ion-icon :icon="camera"></ion-icon>
        </ion-button>

        <ion-button @click="captureCamera()">
          <ion-icon :icon="aperture"></ion-icon>
        </ion-button>

        <ion-button @click="flipCamera()">
          <ion-icon :icon="repeat"></ion-icon>
        </ion-button>
      </ion-toolbar>
    </ion-footer>
  </ion-page>
</template>

<script setup lang="ts">
import { IonPage, IonHeader, IonToolbar, IonTitle, IonContent, IonButton, IonIcon, IonFooter, IonSegment, IonSegmentButton, IonLabel } from '@ionic/vue';
import { CameraPreview, CameraPreviewOptions } from '@capacitor-community/camera-preview';
import { aperture, camera, repeat, stop } from 'ionicons/icons';
import { onMounted, ref } from 'vue';

const cameraPreviewOptions: CameraPreviewOptions = {
  position: 'rear',
  parent: 'camera',
  className: 'camera'
};

const isOn: any = ref(true);
const bodyType: any = ref('Man');
const imageCapture: any = ref();

const segmentChanged = (event: any) => {
  console.log(event);
  bodyType.value = event.target.value
}

const captureCamera = async () => {
  const result = await CameraPreview.capture({ quality: 90 })
  imageCapture.value = `data:image/jpeg;base64,${result.value}`
  stopCamera();
}

const startCamera = () => {
  isOn.value = true
  CameraPreview.start(cameraPreviewOptions);
}

const stopCamera = () => {
  isOn.value = false
  CameraPreview.stop()
}

const flipCamera = () => {
  CameraPreview.flip()
}

onMounted(() => {
  startCamera()
})
</script>

<style scoped lang="scss">
.camera {
  position: relative;
}

.burn-area {
  position: absolute;
  top: 0;
  margin: 0 auto;
  width: 100%;
  height: 100%;

  img {
    width: 100%;
    height: 100%;
    opacity: 0.5;
  }
}
</style>