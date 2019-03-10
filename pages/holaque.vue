<template>
  <div class="holaque" :style="backgroundColor">
    <input type="radio" id="one" value="One" v-model="picked">
    <label for="one">One</label>
    <br>
    <input type="radio" id="two" value="Gradient" v-model="picked">
    <label for="two">Two</label>
    <br>
    <span>Picked: {{ picked }}</span>
    <input v-model="message" placeholder="edit me">
    <svg width="351.640625" height="175.90625" :name="`hola${message}`" ref="svg">
      <defs>
        <style type="text/css">
          @font-face {
            font-family: "Graphik";
            font-style: normal;
            font-display: swap;
            font-weight: 200;
            src: url("/fonts/Graphik-Regular.woff2") format("woff2");
          }
        </style>
        <filter xmlns="http://www.w3.org/2000/svg" id="ejemplo_over">
          <feFlood x="70" y="70" width="100%" height="20" result="img2"></feFlood>
          <feComposite in="SourceGraphic" in2="img2" operator="out"></feComposite>
        </filter>
        <linearGradient id="Gradient">
          <stop offset="0%" stop-color="#FFDC00" />
          <stop offset="100%" stop-color="#E6007D" />
        </linearGradient>
      </defs>
      <g :fill="getLogoColor" filter="url(#ejemplo_over)">
        <text x="0" y="120" ref="text" style="font-size:176px; font-family:'Graphik'">hola{{ message }}</text>
      </g>
    </svg>
    <canvas style="display: none" id="canvas" ref="canvas"></canvas>
    <button @click="downloadPNG">Download</button>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        message: '',
        picked: ''
      }
    },
    computed: {
      backgroundColor () {
        if (this.picked === 'Gradient') return 'background-image: linear-gradient(90deg,#e6007d,#f06c17);'
        return 'background-color: white;'
      },

      getLogoColor () {
        if (this.picked !== 'Gradient') return 'url(#Gradient)'

        return 'white'
      }
    },

    methods: {
      downloadPNG () {
        const canvas = this.$refs.canvas;
        const svg = this.$refs.svg;
        const data = new XMLSerializer().serializeToString(svg);
        const win = window.URL || window.webkitURL || window;
        const img = new Image();
        const blob = new Blob([data], { type: 'image/svg+xml' });
        const url = win.createObjectURL(blob);
        img.onload = function () {
          canvas.getContext("2d").drawImage(img, 0, 0)
          win.revokeObjectURL(url);
          const uri = canvas.toDataURL('image/png').replace('image/png', 'octet/stream');
          const a = document.createElement("a");
          document.body.appendChild(a);
          a.style = "display: none";
          a.href = uri
          a.download = (svg.getAttribute('name') || 'untitled') + '.png';
          a.click();
          window.URL.revokeObjectURL(uri);
          document.body.removeChild(a);
        };
        img.src = url;
      }
    },
    watch: {
      message () {
        const text = this.$refs.text;
        const svg = this.$refs.svg;

        setTimeout(function(){
          const bbox = text.getBBox(); 
          const width = bbox.width;
          const height = bbox.height;
          svg.setAttribute('width', width);
          svg.setAttribute('height', height);
          canvas.setAttribute('width', width);
          canvas.setAttribute('height', height);
        }, 10);
      }
    }
  }
</script>
<style lang="scss">
</style>
