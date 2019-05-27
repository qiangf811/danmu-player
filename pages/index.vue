<template>
  <div class='player' ref="container">
    <canvas ref="canvas" id="canvas"></canvas>
    <video
      controls
      ref="video"
      id='my-player'
      class="video-js"
      :class="isFullscreen?'fullSize':'dfg'">
      <source src="/video/video.mp4" type="video/mp4" />
    </video>
    <h1 style="position: absolute">{{isFullscreen?'全屏':'非全屏'}}</h1>
  </div>
</template>

<script>
  import screenfull from 'screenfull'
  import videojs from 'video.js'
  import CanvasBarrage from '../utils/CanvasBarrage'
  window.videojs = videojs
  require('videojs-flash/dist/videojs-flash.js')
  require('videojs-contrib-hls/dist/videojs-contrib-hls.js')
  export default {
    data () {
      return {
        isFullscreen: false,
        player: null,
        canvasBarrage: null,
        list: [
          { value: '周杰伦的听妈妈的话，让我反复循环再循环', time: 5, color: 'red', speed: 1, fontSize: 22 },
          { value: '想快快长大，才能保护她', time: 10, color: '#00a1f5', speed: 1, fontSize: 30 }
        ],
        playerSize: {
          width: 800,
          height: 420
        }
      }
    },
    mounted () {
      this.player = videojs('my-player', {
        controls: true,
        autoplay: false,
        preload: 'auto',
        ...this.playerSize
      })
      this.newFullScreenBtn()
      // this.insertCanvas()
      this.hanlderScreenFullChange()
      this.initCanvasBarrage()
    },
    methods: {
      hanlderScreenFullChange () {
        screenfull.on('change', () => {
          if (screenfull.isFullscreen) {
            this.isFullscreen = true
            document.getElementById('my-player').style.width = '100%'
            document.getElementById('my-player').style.height = '100%'
            if (this.canvasBarrage) {
              this.canvasBarrage.canvas.width = window.screen.width
              this.canvasBarrage.canvas.height = window.screen.height - 36
            }
          } else {
            this.isFullscreen = false
            if (this.canvasBarrage) {
              this.canvasBarrage.canvas.width = this.playerSize.width
              this.canvasBarrage.canvas.height = this.playerSize.height - 36
            }
          }
        })
      },
      initCanvasBarrage () {
        // debugger
        this.canvasBarrage = new CanvasBarrage(this.$refs.canvas || document.getElementById('canvas'), this.$refs.video, { data: this.list })
        this.player.on('pause', () => {
          // isPaused设为true表示暂停播放
          this.canvasBarrage.isPaused = true
        })

        this.player.on('seeked', () => {
          // 调用CanvasBarrage类的replay方法进行回放，重新渲染弹幕
          this.canvasBarrage.replay()
        })
        this.player.on('play', () => {
          this.canvasBarrage.isPaused = false
          this.canvasBarrage.render() // 触发弹幕
          const adddddd = () => {
            const item = {
              time: Math.random() * 4 * 60,
              value: new Date().getTime(),
              color: new Date().getTime() % 2 === 0 ? 'red' : 'orange',
              speed: Math.random() * 20,
              fontSize: 22
            }
            this.canvasBarrage.add(item)
          }

          var time = 2000
          var interval = setInterval(() => {
            if (time >= 200000) {
              clearInterval(interval)
            } else {
              for (let i = 0; i < 3333; i++) {
                requestAnimationFrame(adddddd)
                time = time + 2000
              }
            }
          }, time)
        })
      },
      toggleFullScreen () {
        if (!screenfull.enabled) {
          this.$message({
            message: 'Your browser does not support!',
            type: 'warning'
          })
          return false
        }
        screenfull.toggle(this.$refs.container)
      },
      newFullScreenBtn () {
        var newbtn = document.createElement('btn')
        newbtn.innerHTML = '<button class="vjs-control" id="downloadButton">全屏</button>'
        var controlBar = document.getElementsByClassName('vjs-control-bar')[0]
        var insertBeforeNode = document.getElementsByClassName('vjs-fullscreen-control')[0]
        controlBar.insertBefore(newbtn, insertBeforeNode)
        newbtn.addEventListener('click', () => {
          this.toggleFullScreen()
        })
      },
      insertCanvas () {
        var canvas = document.createElement('canvas')
        canvas.setAttribute('id', 'canvas')
        var videoContainer = document.getElementById('my-player')
        var insertBeforeNode = document.getElementById('my-player_html5_api')
        videoContainer.insertBefore(canvas, insertBeforeNode)
      }
    }
  }
</script>

<style>
  /* @import 'video.js/dist/video-js.min.css'; */

  * {
    margin: 0;
    padding: 0
  }

  .player {
    width: 800px;
    height: auto;
    margin: 0 auto;
    position: relative;
    background-color: black;
  }

  .player video {
    width: 100%;
    height: 100%;
  }

  .video-status {
    cursor: pointer;
    position: absolute;
    right: 54px;
    bottom: 35px;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    border: 1px solid #fff;
    color: #fff;
    /* opacity: 0; */
  }

  canvas {
    position: absolute;
    z-index: 99;
  }

  .video-status img {
    width: 12px;
    height: 12px;
  }

  .full-screen {
    position: fixed;
    width: 100%;
    height: 100%;
  }
  .fullSize{
    width: 100%;
    height: 100%;
  }
  .video-js .vjs-fullscreen-control {
    display: none !important;
  }
  .video-js .vjs-tech{
    position: relative;
  }
  .video-js .vjs-big-play-button{
    bottom: 10px;
    top: auto;
  }
</style>