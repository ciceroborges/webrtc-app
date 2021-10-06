<template>
  <div>
    <label>Câmeras disponíveis: </label><select id="camera-list"/>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  mounted() {
    // pedir permissao para usar os dispositivos de audio e video
    try {
        const stream = this.openMediaDevices({'video':true,'audio':true});
        console.log('Got MediaStream:', stream);
    } catch(error) {
        console.error('Error accessing media devices.', error);
    }

    // exibe e armazena a lista de dispositivos conectados
    const videoCameras = this.getConnectedDevices('videoinput')
          .then((res) => this.updateCameraList(res));
        console.log('Cameras found:', videoCameras);

    // escuta mudanças nos dispositivos e atualiza a lista
    navigator.mediaDevices.addEventListener('devicechange', event => {
        const newCameraList = this.getConnectedDevices('video')
          .then((res) => this.updateCameraList(res));
        console.log(newCameraList);
        console.log(event);
    });
  },
  methods: {
    // acessar cameras ou microfones conectados no pc / celular
    async openMediaDevices(constraints) {
      return await navigator.mediaDevices.getUserMedia(constraints);
    },
    // verificar todas as câmeras e microfones conectados
    async getConnectedDevices(type) {
      const devices = await navigator.mediaDevices.enumerateDevices();
      return devices.filter(device => device.kind === type)
    },
    // Atualiza a lista de cameras
    updateCameraList(cameras) {
        const listElement = document.getElementById('camera-list');
        listElement.innerHTML = '';
        console.log(cameras);
        cameras.map(camera => {
            const cameraOption = document.createElement('option');
            cameraOption.label = camera.label;
            cameraOption.value = camera.deviceId;
            listElement.add(cameraOption)
        });
        
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
