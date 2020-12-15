<template>
  <v-app>
    <v-main>
        <div :style="{backgroundImage: `url(${form.background})`}" style="filter: blur(2px);-webkit-filter: blur(4px);height: 100%;background-size: cover;">
        </div>

        <div class="bg-text">
          <div class="text-h2" v-text="form.title" />
          <div v-text="form.quote" />
          <div v-text="form.author" />
        </div>
    </v-main>
  </v-app>
</template>

<script>
import net from 'net'

export default {
  data() {
    return {
      ip: 'localhost',
      port: '13337',
      socket: null,
      form: {
        title: '',
        quote: '',
        author: '',
        date: '',
        background: '',
      },
      error: {
        msg: ''
      },
    }
  },

  created() {
    this.connect() 
  },

  methods: {
    async connect() {
      const socket = new net.Socket()
      this.socket = socket
      await this.socket.connect(this.port, this.ip)
      socket.on('data', (data) => {
        const jsonstr = new TextDecoder('utf-8').decode(data)
        const json = JSON.parse(jsonstr)
        if(json.error) {
          this.error.msg = json.error.message
          return
        }
        this.form.title = json.contents.quotes[0].title
        this.form.quote = json.contents.quotes[0].quote
        this.form.author = json.contents.quotes[0].author
        this.form.date = json.contents.quotes[0].date
        this.form.background = json.contents.quotes[0].background
      })
    }, 
  }
}
</script>

<style>
body, html {
  height: 100%;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}

* {
  box-sizing: border-box;
}

.bg-image {
  /* Add the blur effect */
  filter: blur(8px);
  -webkit-filter: blur(8px);
  
  /* Full height */
  height: 100%; 
  
  /* Center and scale the image nicely */
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

/* Position text in the middle of the page/image */
.bg-text {
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0, 0.4); /* Black w/opacity/see-through */
  color: white;
  font-weight: bold;
  border: 3px solid #f1f1f1;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  width: 80%;
  padding: 20px;
  text-align: center;
}
</style>



