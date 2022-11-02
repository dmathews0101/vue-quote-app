<template>
  <div id="app">
    <transition name="fadeinbackgroundimage">
      <div v-if="showbackgroundimage" class="flex-container" :class="selectedBackground">
        <transition name="bounce">
          <div v-if="showtransparentbox" :class="{'transparentbox' : transparentboxx}">
            <div :class="{'insidebox' : insideboxx, 'insideboxp' : insideboxxp}">
              <div class="quote-content">
                <transition name="rollinquote">
                  <h1 v-if="showquote" class="quote">&ldquo; {{quote.quote}} &rdquo;</h1>
                </transition>
                <br />
                <transition name="rolloutquoteauthor">
                  <h2 v-if="showquoteauthor" class="quoteauthor">&#8764; {{quote.author}}</h2>
                </transition>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </transition>
  </div>
</template>


<script>
import { Quotes } from './quotes'
import { ConfigLoader } from '@igappstore/core'

export default {
  name: 'App',
  data() {
    return {
      showbackgroundimage: false,
      showtransparentbox: false,
      showquote: false,
      showquoteauthor: false,
      config: window.MicroAppConfiguration,
      transparentboxx: true,
      insideboxx: false,
      insideboxxp: false,
      topicquotes: '',
      urlparsed: '',
      tempconfig: {},
      topic: '',
      duration: '',
      background: '',
      allquotes: Quotes,
      previousquote: '',
      quote: '',
      random: false,
      loader: false,
      errorMessage: '',
      randomQuote: '',
      snackbar: false,
      selectedBackground: 'sea',
      previousselectedBackground: 'sea',
      backgrounds: ['branch', 'flowers', 'shallowwater', 'tiles', 'wood'],
      backgroundsil: ['butterfly', 'flower', 'giantwheel', 'plants', 'sea'],
      backgroundsip: ['dolphin', 'feather', 'man', 'sky'],
      timer: null,
    }
  },
  methods: {
    checkTopicInUrl() {
      this.tempconfig['topic'] = this.urlparsed['topic'] || 'inspirational'

      // If topic exists in URL
      if (this.urlparsed['topic']) {
        this.tempconfig['topic'] = this.urlparsed['topic']
      } else {
        this.tempconfig['topic'] = 'inspirational'
      }
    },

    async checkUrl() {
      const config = await ConfigLoader.loadConfig()
      this.config = config
      this.topic = this.config.topic
      this.duration = this.config.duration
      this.background = this.config.background
      this.topicquotes = this.allquotes[0][this.topic]
    },
    togglebackgroundimage() {
      this.showbackgroundimage = !this.showbackgroundimage
    },
    toggleshowtransparentbox() {
      this.showtransparentbox = !this.showtransparentbox
    },
    toggleshowquote() {
      this.showquote = !this.showquote
    },
    toggleshowquoteauthor() {
      this.showquoteauthor = !this.showquoteauthor
    },
    refreshQuote() {
      setTimeout(
        function () {
          this.togglebackgroundimage()
        }.bind(this),
        0
      )
      setTimeout(
        function () {
          this.toggleshowtransparentbox()
        }.bind(this),
        600
      )
      setTimeout(
        function () {
          this.toggleshowquote()
        }.bind(this),
        1300
      )
      setTimeout(
        function () {
          this.toggleshowquoteauthor()
        }.bind(this),
        2000
      )
      setTimeout(
        function () {
          this.toggleshowquoteauthor()
        }.bind(this),
        this.duration - 3000
      )
      setTimeout(
        function () {
          this.toggleshowquote()
        }.bind(this),
        this.duration - 2000
      )
      setTimeout(
        function () {
          this.toggleshowtransparentbox()
        }.bind(this),
        this.duration - 1500
      )
      setTimeout(
        function () {
          this.togglebackgroundimage()
        }.bind(this),
        this.duration - 1000
      )

      if (this.background == 'pattern') {
        this.randomBackground()
      } else if (this.background == 'image') {
        if (window.innerWidth > window.innerHeight) {
          this.randomBackgroundil()
        } else {
          this.randomBackgroundip()
        }
      } else {
        if (window.innerWidth > window.innerHeight) {
          this.randomBackgroundil()
        } else {
          this.randomBackgroundip()
        }
      }
      this.previousquote = this.quote
      this.quote = ''
      if (this.topicquotes) {
        this.quote = this.topicquotes[
          Math.floor(Math.random() * this.topicquotes.length)
        ]
        while (this.quote == this.previousquote) {
          this.quote = this.topicquotes[
            Math.floor(Math.random() * this.topicquotes.length)
          ]
        }
      } else {
        this.topicquotes = this.allquotes[0]['inspirational']
        this.quote = this.topicquotes[
          Math.floor(Math.random() * this.topicquotes.length)
        ]
        while (this.quote == this.previousquote) {
          this.quote = this.topicquotes[
            Math.floor(Math.random() * this.topicquotes.length)
          ]
        }
      }
    },
    randomBackground() {
      this.previousselectedBackground = this.selectedBackground
      this.selectedBackground = this.backgrounds[
        Math.floor(Math.random() * this.backgrounds.length)
      ]
      while (this.selectedBackground == this.previousselectedBackground) {
        this.selectedBackground = this.backgrounds[
          Math.floor(Math.random() * this.backgrounds.length)
        ]
      }
    },
    randomBackgroundil() {
      this.previousselectedBackground = this.selectedBackground
      this.selectedBackground = this.backgroundsil[
        Math.floor(Math.random() * this.backgroundsil.length)
      ]
      while (this.selectedBackground == this.previousselectedBackground) {
        this.selectedBackground = this.backgroundsil[
          Math.floor(Math.random() * this.backgroundsil.length)
        ]
      }
    },
    randomBackgroundip() {
      this.previousselectedBackground = this.selectedBackground
      this.selectedBackground = this.backgroundsip[
        Math.floor(Math.random() * this.backgroundsip.length)
      ]
      while (this.selectedBackground == this.previousselectedBackground) {
        this.selectedBackground = this.backgroundsip[
          Math.floor(Math.random() * this.backgroundsip.length)
        ]
      }
    },
  },
  created() {
    if (this.topicquotes) {
      this.quote = this.topicquotes[
        Math.floor(Math.random() * this.topicquotes.length)
      ]
    } else {
      this.topicquotes = this.allquotes[0]['inspirational']
      this.quote = this.topicquotes[
        Math.floor(Math.random() * this.topicquotes.length)
      ]
    }
  },
  async mounted() {
    if (window.innerWidth > window.innerHeight) {
      this.insideboxx = true
      this.insideboxxp = false
    } else {
      this.insideboxx = false
      this.insideboxxp = true
    }
    await this.checkUrl()
    this.refreshQuote()
    this.timer = setInterval(() => this.refreshQuote(), this.duration)
    window.onresize = resize
    function resize() {
      if (window.innerWidth > window.innerHeight) {
        location.reload()
        this.insideboxx = true
        this.insideboxxp = false
      } else {
        location.reload()
        this.insideboxx = false
        this.insideboxxp = true
      }
    }
  },
  beforeDestroy() {
    clearInterval(this.timer)
  },
}
</script>
<style>
@font-face {
  src: url(assets/fonts/Alegreya-ExtraBoldItalic.ttf);
  font-family: AlegreyaQ;
}
@font-face {
  src: url(assets/fonts/Alegreya-Italic.ttf);
  font-family: AlegreyaQa;
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  width: 100vw;
  height: 100vh;
  font-family: AlegreyaQ;
  background-color: #263238;
}
.flex-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
}

.transparentbox {
  position: relative;
  width: 85vw;
  height: 85vh;
  background: rgba(255, 255, 255, 0.2);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
  color: black;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}
.insidebox {
  width: 75vw;
  height: 75vh;
  margin: auto;
  font-size: 2.5vw;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  flex-direction: column;
}
.insideboxp {
  width: 75vw;
  height: 75vh;
  margin: auto;
  font-size: 2.5vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  flex-direction: column;
}
.quote-content {
  font-family: 'AlegreyaQ', Arial, sans-serif;
  display: block;
  width: 70vw;
  height: 40vh;
  text-align: center;
  color: black;
  margin: auto;
}
.insidebox .quote {
  font-family: 'AlegreyaQ', Arial, sans-serif;
  font-size: 4.2vw;
}
.insideboxp .quote {
  font-family: 'AlegreyaQ', Arial, sans-serif;
  font-size: 4.2vh;
}

.insidebox .quoteauthor {
  font-family: 'AlegreyaQa', Arial, sans-serif;
  font-size: 3.9vw;
}
.insideboxp .quoteauthor {
  font-family: 'AlegreyaQa', Arial, sans-serif;
  font-size: 3.9vh;
}

/* Pattern */
.branch {
  background-image: url('assets/backgroundimg/pattern/branch.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.flowers {
  background-image: url('assets/backgroundimg/pattern/flowers.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.shallowwater {
  background-image: url('assets/backgroundimg/pattern/shallowwater.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.tiles {
  background-image: url('assets/backgroundimg/pattern/tiles.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.wood {
  background-image: url('assets/backgroundimg/pattern/wood.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

/* Landscape */
.butterfly {
  background-image: url('assets/backgroundimg/landscape/butterfly.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.flower {
  background-image: url('assets/backgroundimg/landscape/flower.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.giantwheel {
  background-image: url('assets/backgroundimg/landscape/giantwheel.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.plants {
  background-image: url('assets/backgroundimg/landscape/plants.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.sea {
  background-image: url('assets/backgroundimg/landscape/sea.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
/* Portrait */
.dolphin {
  background-image: url('assets/backgroundimg/portrait/dolphin.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.feather {
  background-image: url('assets/backgroundimg/portrait/feather.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.man {
  background-image: url('assets/backgroundimg/portrait/man.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.sky {
  background-image: url('assets/backgroundimg/portrait/sky.jpg');
  background-color: #cccccc;
  height: 100vh;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

/* -Roll In - Quote---------------- */
.rollinquote-enter-active {
  animation: rollInQuote 0.6s;
}
.rollinquote-leave-active {
  animation: rollInQuote 0.6s reverse;
}

@keyframes rollInQuote {
  from {
    opacity: 0;
    transform: translate3d(-100%, 0, 0) rotate3d(0, 0, 1, -120deg);
  }

  to {
    opacity: 1;
    transform: translate3d(0, 0, 0);
  }
}

/* -Roll Out - Quote Author------------------- */
.rolloutquoteauthor-enter-active {
  animation: rollOutQuoteAuthor 0.6s reverse;
}
.rolloutquoteauthor-leave-active {
  animation: rollOutQuoteAuthor 0.6s;
}

@keyframes rollOutQuoteAuthor {
  from {
    opacity: 1;
  }

  to {
    opacity: 0;
    transform: translate3d(100%, 0, 0) rotate3d(0, 0, 1, 120deg);
  }
}

/* -Fade In BackgroundImage*/
.fadeinbackgroundimage-enter-active {
  animation: fadeInBackgroundImage 0.6s;
}
.fadeinbackgroundimage-leave-active {
  animation: fadeInBackgroundImage 0.6s reverse;
}

@keyframes fadeInBackgroundImage {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

/* -Bounce In - Transparent Box---------------------------- */
.bounce-enter-active {
  animation: bounce-in 0.6s;
}
.bounce-leave-active {
  animation: bounce-in 0.6s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>
