<template>
  <div class='container'>
    <player
      ref="videoPlayer"
      :video-url="videoUrl"
      :error="videoError"
      :message="videoError"
      :height="200"
      :has-audio="false"
      fluent
      autoplay
      live
      muted
    />
  </div>
</template>

<script>
import player from '@/components/jessibucaPlayer/jessibuca.vue'
export default {
  name: 'Index',
  components: {
    player
  },
  data() {
    return {
      videoUrl: '视频播放地址'
    }
  },
  methods: {
    /**
     * 视频播放错误
     */
    videoError(e) {
      console.log(`播放器错误：${JSON.stringify(e)}`)
    }
  }
}
</script>

<style lang='scss' scoped>
</style>