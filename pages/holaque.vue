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
    <svg width="100%" height="100%">
      <defs>
        <filter xmlns="http://www.w3.org/2000/svg" id="ejemplo_over">
          <feFlood x="70" y="70" width="100%" height="20" result="img2"></feFlood>
          <feComposite in="SourceGraphic" in2="img2" operator="out"></feComposite>
        </filter>
      </defs>
      <defs>
        <linearGradient id="Gradient">
          <stop offset="0%" stop-color="#FFDC00" />
          <stop offset="100%" stop-color="#E6007D" />
        </linearGradient>
      </defs>
      <g :fill="getLogoColor" filter="url(#ejemplo_over)">
        <text x="100" y="120" style="font-size:176px;">hola{{ message }}</text>
      </g>
    </svg>
    <canvas id="canvas" ref="canvas" width="106" height="122"></canvas>
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
    mounted () {

     let ctx = this.$refs['canvas'].getContext('2d')

      function svg_en_canvas(svgElement, ctx, callback){
        var svgURL = new XMLSerializer().serializeToString(svgElement);
        //pinta la imágen en el canvas
        var img  = new Image();
        img.onload = function(){
          ctx.drawImage(this, 0,0);
          callback();
          }
        img.src = 'data:image/svg+xml; charset=utf8, '+encodeURIComponent(svgURL);
        }

      // svg_en_canvas(document.querySelector('svg'), ctx, ()=>{
      //   var img = canvas.toDataURL("image/png");
      //   document.write('<a href="'+img+'" download><img src="'+img+'"></a>');
      // })
    }
  }
</script>
<style lang="scss">
</style>
