<template>
  <div class="hello">
    <p>{{ error }}</p>
    <div class="con">
      <qrcode-stream class="qr-code-scanner" @decode="onDecode" @init="onInit"></qrcode-stream>
    </div>
  </div>
</template>

<script>
import { QrcodeStream } from 'vue3-qrcode-reader'
import SocketioService from './services/socketio.service.js';
export default {
  data() {
    return {
      error: '',
    }
  },
  components: {
    QrcodeStream
  },
  methods: {
    async onInit(promise) {
      try {
        const { capabilities } = await promise
        console.log(capabilities)
        // successfully initialized
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.error = "user denied camera access permisson"
        } else if (error.name === 'NotFoundError') {
          this.error = "no suitable camera device installed"
        } else if (error.name === 'NotSupportedError') {
          this.error = "page is not served over HTTPS (or localhost)"
        } else if (error.name === 'NotReadableError') {
          this.error = "maybe camera is already in use"
        } else if (error.name === 'OverconstrainedError') {
          this.error = "did you requested the front camera although there is none?"
        } else if (error.name === 'StreamApiNotSupportedError') {
          this.error = "browser seems to be lacking features"
        }
      } finally {
        // hide loading indicator
      }
    },
    onDecode(decodedString) {
      console.log("decodedString", decodedString)
    }
  },
  created() {
    SocketioService.setupSocketConnection();
  },
  beforeUnmount() {
    SocketioService.disconnect();
  },
  messageChannel(data) {
      this.socketMessage = data
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  display: flex;
  align-content: center;
  align-items: center;
  justify-content: center;
}
.con {
  width: 300px;
  height: 300px;
}

.qr-code-scanner{
  border: 2px solid #42b983;
  border-style: dashed;
  border-radius: 5px;
}

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
