<template>
  <div class="holalogo">
    <div class="holalogo__sidebar">
      <LangSwitcher />
      <div class="holalogo__sidebar-container">
        <h2 class="holalogo__h2" >
          Holalogo
        </h2>
        <p v-html="$t('holalogo.description')"/>
        <label class="switch">
          <input
            type="checkbox"
            v-model="white"
          >
          <span class="slider round"/>
        </label>
        <div class="text-input holalogo__text-input">
          <label
            v-html="$t('holalogo.label')"
            class="text-input__label"
          />
          <input
            class="text-input__field"
            v-model="message"
            :placeholder="$t('holalogo.placeholder')"
          >
        </div>
        <button
          class="holalogo__button"
          @click="downloadPNG"
        >
          {{ $t('holalogo.download') }} PNG ({{Math.floor(viewBoxWidth * 4)}}px x {{Math.floor(viewBoxHeight * 4)}}px)
        </button>
        <p v-html="$t('holalogo.disclaimer')"/>
      </div>
    </div>
    <div 
      class="holalogo__view"
      :class="{ 'holalogo__view--color' : white }"
    >
      <div class="holalogo__svg-container">
        <svg
          class="holalogo__svg"
          :name="`hola${message}`"
          ref="svg"
          :viewBox="`0 0 ${viewBoxWidth} ${viewBoxHeight}`"
        >
          <SVGFontLoader />
          <style type="text/css">
            .holalogo__svg-text {
              font-size: 176px;
              font-family:'Ciutadella';
              letter-spacing: -1.9px;
              text-transform: lowercase;
            }
          </style>
          <defs>
            <filter xmlns="http://www.w3.org/2000/svg" id="cutting_line">
              <feFlood 
                x="0" y="130"
                width="100%"
                height="18"
                result="cuttingLine"
              />
              <feComposite
                in="SourceGraphic"
                in2="cuttingLine"
                operator="out"
              />
            </filter>
            <linearGradient id="Gradient">
              <stop offset="0%" stop-color="#FFDC00" />
              <stop offset="100%" stop-color="#E6007D" />
            </linearGradient>
          </defs>
          <g
            :fill="getLogoColor"
            filter="url(#cutting_line)"
          >
            <text
              x="0" y="181"
              ref="text"
              class="holalogo__svg-text"
            >
              hola{{ message }}
            </text>
          </g>
        </svg>
      </div>
      <div
        class="holalogo__footer"
        v-html="$t('holalogo.footer')"
      />
      <canvas
        style="display:none;"
        id="canvas"
        ref="canvas"
      />
    </div>
  </div>
</template>
<script>
  import SVGFontLoader from '~/components/SVGFontLoader'
  import LangSwitcher from '~/components/LangSwitcher'

  export default {
    layout: 'full-screen',

    data () {
      return {
        message: '',
        white: false,
        viewBoxWidth: 300,
        viewBoxHeight: 229
      }
    },

    components: {
      SVGFontLoader,
      LangSwitcher
    },

    head () {
      return {
        title: 'Holalogo',
        htmlAttrs: {
          lang: this.$i18n.locale,
        },
        meta: [
          { name: "author", content: "Marina Aisa" },
          { name: "description", property: "og:description", content: this.$t('holalogo.description'), hid: "description" },
          { property: "og:title", content: 'Holalogo' },
          { property: "og:image", content: this.ogImage },
          { name: "twitter:description", content: this.$t('holalogo.description') },
          { name: "twitter:image", content: this.ogImage }
        ]
      };
    },

    computed: {
      getLogoColor () {
        if (this.white) return 'white'
        return 'url(#Gradient)'
      },
      ogImage () {
        return `${process.env.baseUrl}/images/holalogo.jpg`;
      }
    },

    methods: {
      downloadPNG () {
        const canvas = this.$refs.canvas
        const svg = this.$refs.svg
        svg.setAttribute('width', this.viewBoxWidth * 4)
        canvas.setAttribute('width', this.viewBoxWidth * 4)
        canvas.setAttribute('height', this.viewBoxHeight * 4)
        const data = new XMLSerializer().serializeToString(svg)
        const win = window.URL || window.webkitURL || window
        const img = new Image()
        const blob = new Blob([data], { type: 'image/svg+xml' })
        const url = win.createObjectURL(blob)
        img.onload = function () {
          canvas.getContext("2d").drawImage(img, 0, 0)
          const uri = canvas.toDataURL('image/png').replace('image/png', 'octet/stream')
          const a = document.createElement("a")
          document.body.appendChild(a)
          a.style = "display: none"
          a.href = uri
          a.download = (svg.getAttribute('name') || 'untitled') + '.png'
          a.click()
          document.body.removeChild(a)
        }
        img.src = url
      }
    },
    watch: {
      message () {
        const text = this.$refs.text
        const svg = this.$refs.svg
        var self = this

        setTimeout(function(){
          const bbox = text.getBBox()
          self.viewBoxWidth = bbox.width
          self.viewBoxHeight = bbox.height
        }, 10)
      }
    }
  }
</script>

<style lang="scss">
.holalogo {
  display: flex;
  align-items: center;
  align-items: stretch;
  flex-direction: column;

  @media (min-width: $screen-md){
    justify-content: center;
    flex-direction: row;
  }

  &__svg {
    width: 100%;

    @media (min-width: $screen-md){
      height: 30rem;
    }
  }

  &__h2 {
    padding-bottom: 24px;
    margin: 0;
  }

  &__sidebar {
    display: flex;
    flex-direction: column;
    display: flex;
    align-items: flex-start;
    padding: 20px;
    box-shadow: 0 0 24px 10px #00000011;
    z-index: 1;

    @media (min-width: $screen-md){
      width: 500px;
    }
  }

  &__sidebar-container {
    flex: 1;
    display: flex;
    justify-content: center;
    flex-direction: column;
  }

  &__view {
    background: white;
    align-items: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    width: 100%;
    position: relative;

    &--color {
      background-image: linear-gradient(90deg,#e6007d,#f06c17);
    }
  }

  &__footer {
    box-shadow: 0 0 24px 10px #00000011;
    background: white;
    padding: 24px;
    margin: 20px;
  }

  &__svg-container {
    flex: 1;
    display: flex;
    align-items: center;
    padding: 24px;
    width: 100%;
  }

  &__text-input {
    width: 100%;
  }

  &__button {
    margin: 24px 0 10px 0;
    width: 100%;
  }
}

.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
  margin-bottom: 24px;

  input { 
    opacity: 0;
    width: 0;
    height: 0;
  }
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: white;
  border: 1px solid $grey-3;
  -webkit-transition: .4s;
  transition: .4s;

  &:before {
    position: absolute;
    content: "";
    height: 26px;
    width: 26px;
    left: 4px;
    bottom: 4px;
    background-color: $primary;
    -webkit-transition: .4s;
    transition: .4s;
  }

  &.round {
    border-radius: 34px;

    &:before {
      border-radius: 50%;
    }
  }
}

input {
  &:checked + .slider {
    background-color: $primary;
    border-color: $primary;

    &:before {
      background-color: white;
      -webkit-transform: translateX(26px);
      -ms-transform: translateX(26px);
      transform: translateX(26px);
    }
  }
}
</style>
