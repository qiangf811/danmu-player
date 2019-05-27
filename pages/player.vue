<template>
    <div id="dplayer" style="height:400px;width:800px"></div>
</template>
<script>
    import 'dplayer/dist/DPlayer.min.css'
    import DPlayer from 'dplayer'
    export default {
      mounted () {
        const dp = new DPlayer({
          container: document.getElementById('dplayer'),
          screenshot: true,
          video: {
            url: '/video/video.mp4',
            pic: 'demo.jpg',
            thumbnails: 'thumbnails.jpg'
          },
          subtitle: {
            url: 'webvtt.vtt'
          },
          danmaku: {
            id: 'demo',
            api: 'https://api.prprpr.me/dplayer/'
          }
        })
      }
    }
</script>