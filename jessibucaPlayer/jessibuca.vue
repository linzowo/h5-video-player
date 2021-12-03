<template>
  <div
    id="jessibuca"
    style="width: auto; height: auto"
  >
    <div
      id="container"
      ref="container"
      style="width: 100%; height: 10rem; background-color: #000"
      @dblclick="fullscreenSwich"
    >
      <div
        id="buttonsBox"
        class="buttons-box"
      >
        <div class="buttons-box-left">
          <i
            v-if="!playing"
            class="iconfont icon-play jessibuca-btn"
            @click="playBtnClick"
          />
          <i
            v-if="playing"
            class="iconfont icon-pause jessibuca-btn"
            @click="pause"
          />
          <i
            class="iconfont icon-stop jessibuca-btn"
            @click="destroy"
          />
          <i
            v-if="isNotMute"
            class="iconfont icon-audio-high jessibuca-btn"
            @click="jessibuca.mute()"
          />
          <i
            v-if="!isNotMute"
            class="iconfont icon-audio-mute jessibuca-btn"
            @click="jessibuca.cancelMute()"
          />
        </div>
        <div class="buttons-box-right">
          <span class="jessibuca-btn">{{ kBps }} kb/s</span>
          <!--          <i class="iconfont icon-file-record1 jessibuca-btn"></i>-->
          <!--          <i class="iconfont icon-xiangqing2 jessibuca-btn" ></i>-->
          <i
            class="iconfont icon-camera1196054easyiconnet jessibuca-btn"
            @click="jessibuca.screenshot('截图','png',0.5)"
          />
          <i
            class="iconfont icon-shuaxin11 jessibuca-btn"
            @click="playBtnClick"
          />
          <i
            v-if="!fullscreen"
            class="iconfont icon-weibiaoti10 jessibuca-btn"
            @click="fullscreenSwich"
          />
          <i
            v-if="fullscreen"
            class="iconfont icon-weibiaoti11 jessibuca-btn"
            @click="fullscreenSwich"
          />
        </div>
      </div>

    </div>
  </div>
</template>

<script>
/* eslint-disable no-underscore-dangle */
export default {
  name: 'Jessibuca',
  props: ['videoUrl', 'error', 'hasAudio', 'height'],
  data() {
    return {
      jessibuca: null,
      playing: false,
      isNotMute: false,
      quieting: false,
      fullscreen: false,
      loaded: false, // mute
      speed: 0,
      performance: '', // 工作情况
      kBps: 0,
      btnDom: null,
      videoInfo: null,
      volume: 1,
      rotate: 0,
      vod: true, // 点播
      forceNoOffscreen: false
    }
  },
  watch: {
    videoUrl: {
      handler(newData, oldData) {
        this.play(newData)
      },
      immediate: true
    }
  },
  mounted() {
    window.onerror = (msg) => {
      // console.error(msg)
    }
    const paramUrl = decodeURIComponent(this.$route.params.url)
    this.$nextTick(() => {
      const dom = document.getElementById('container')
      dom.style.height = `${(9 / 16) * dom.clientWidth}px`
      if (typeof this.videoUrl === 'undefined') {
        this.videoUrl = paramUrl
      }
      this.btnDom = document.getElementById('buttonsBox')
      console.log(`初始化时的地址为: ${this.videoUrl}`)
      this.play(this.videoUrl)
    })
  },
  destroyed() {
    if (this.jessibuca) {
      this.jessibuca.destroy()
    }
    this.playing = false
    this.loaded = false
    this.performance = ''
  },
  methods: {
    create() {
      const options = {}
      console.log(this.$refs.container)
      console.log(`hasAudio  ${this.hasAudio}`)

      this.jessibuca = new window.Jessibuca(
        Object.assign(
          {
            container: this.$refs.container,
            videoBuffer: 0.5, // 最大缓冲时长，单位秒
            isResize: true,
            isFlv: true,
            decoder: '/jessibuca/index.js',
            // text: "WVP-PRO",
            // background: "bg.jpg",
            loadingText: '加载中',
            hasAudio:
              typeof this.hasAudio === 'undefined' ? true : this.hasAudio,
            debug: false,
            supportDblclickFullscreen: false, // 是否支持屏幕的双击事件，触发全屏，取消全屏事件。
            operateBtns: {
              fullscreen: false,
              screenshot: false,
              play: false,
              audio: false
            },
            record: 'record',
            vod: this.vod,
            forceNoOffscreen: this.forceNoOffscreen,
            isNotMute: this.isNotMute
          },
          options
        )
      )

      // eslint-disable-next-line no-underscore-dangle
      const _this = this
      this.jessibuca.on('load', () => {
        console.log('on load init')
      })

      this.jessibuca.on('log', (msg) => {
        console.log('on log', msg)
      })
      this.jessibuca.on('record', (msg) => {
        console.log('on record:', msg)
      })
      this.jessibuca.on('pause', () => {
        _this.playing = false
      })
      this.jessibuca.on('play', () => {
        _this.playing = true
      })
      this.jessibuca.on('fullscreen', (msg) => {
        console.log('on fullscreen', msg)
        _this.fullscreen = msg
      })

      this.jessibuca.on('mute', (msg) => {
        console.log('on mute', msg)
        _this.isNotMute = !msg
      })
      this.jessibuca.on('audioInfo', (msg) => {
        // console.log("audioInfo", msg);
      })

      this.jessibuca.on('videoInfo', function (msg) {
        this.videoInfo = msg
        // console.log("videoInfo", msg);
      })

      this.jessibuca.on('bps', (bps) => {
        // console.log('bps', bps);
      })
      // eslint-disable-next-line no-unused-vars
      let _ts = 0
      this.jessibuca.on('timeUpdate', (ts) => {
        // console.log('timeUpdate,old,new,timestamp', _ts, ts, ts - _ts);
        _ts = ts
      })

      this.jessibuca.on('videoInfo', (info) => {
        console.log('videoInfo', info)
      })

      this.jessibuca.on('error', (error) => {
        console.log('error', error)
      })

      this.jessibuca.on('timeout', () => {
        console.log('timeout')
      })

      this.jessibuca.on('start', () => {
        console.log('start')
      })

      this.jessibuca.on('performance', (performance) => {
        let show = '卡顿'
        if (performance === 2) {
          show = '非常流畅'
        } else if (performance === 1) {
          show = '流畅'
        }
        _this.performance = show
      })
      this.jessibuca.on('buffer', (buffer) => {
        // console.log('buffer', buffer);
      })

      this.jessibuca.on('stats', (stats) => {
        // console.log('stats', stats);
      })

      this.jessibuca.on('kBps', (kBps) => {
        _this.kBps = Math.round(kBps)
      })

      // 显示时间戳 PTS
      this.jessibuca.on('videoFrame', () => {})

      //
      this.jessibuca.on('metadata', () => {})
    },
    playBtnClick(event) {
      this.play(this.videoUrl)
    },
    play(url) {
      console.log(url)
      if (this.jessibuca) {
        this.destroy()
      }
      this.create()
      this.jessibuca.on('play', () => {
        this.playing = true
        this.loaded = true
        this.quieting = this.jessibuca.quieting
      })
      if (this.jessibuca.hasLoaded()) {
        this.jessibuca.play(url)
      } else {
        this.jessibuca.on('load', () => {
          console.log('load 播放')
          this.jessibuca.play(url)
        })
      }
    },
    pause() {
      if (this.jessibuca) {
        this.jessibuca.pause()
      }
      this.playing = false
      this.err = ''
      this.performance = ''
    },
    destroy() {
      if (this.jessibuca) {
        this.jessibuca.destroy()
      }
      if (document.getElementById('buttonsBox') == null) {
        document.getElementById('container').appendChild(this.btnDom)
      }
      this.jessibuca = null
      this.playing = false
      this.err = ''
      this.performance = ''
    },
    eventcallbacK(type, message) {
      // console.log("player 事件回调")
      // console.log(type)
      // console.log(message)
    },
    fullscreenSwich() {
      const isFull = this.isFullscreen()
      this.jessibuca.setFullscreen(!isFull)
      this.fullscreen = !isFull
    },
    isFullscreen() {
      return (
        document.fullscreenElement ||
        document.msFullscreenElement ||
        document.mozFullScreenElement ||
        document.webkitFullscreenElement ||
        false
      )
    }
  }
}
</script>

<style>
@font-face {
  font-family: 'iconfont'; /* Project id 1291092 */
  src: url('iconfont.woff2?t=1637741914969') format('woff2');
}

.iconfont {
  font-family: 'iconfont' !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-play:before {
  content: '\e603';
}

.icon-pause:before {
  content: '\e6c6';
}

.icon-stop:before {
  content: '\e6a8';
}

.icon-audio-high:before {
  content: '\e793';
}

.icon-audio-mute:before {
  content: '\e792';
}

.icon-shuaxin11:before {
  content: '\e720';
}

.icon-weibiaoti10:before {
  content: '\e78f';
}

.icon-weibiaoti11:before {
  content: '\e790';
}

.icon-camera1196054easyiconnet:before {
  content: '\e791';
}

.buttons-box {
  width: 100%;
  height: 50px;
  background-color: rgba(43, 51, 63, 0.7);
  position: absolute;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  left: 0;
  bottom: 0;
  user-select: none;
  z-index: 10;
  display: flex;
  align-items: center;
}

.buttons-box-left {
  display: flex;
  flex: 1;
  align-items: center;
}
.jessibuca-btn {
  color: rgb(255, 255, 255);
  line-height: 27px;
  margin: 0px 10px;
  padding: 0px 2px;
  cursor: pointer;
  text-align: center;
  font-size: 20px !important;
}
.buttons-box-right {
  height: 100%;
  position: absolute;
  right: 0;
  display: flex;
  flex: 1;
  align-items: center;
  font-size: 20px;
}
</style>
