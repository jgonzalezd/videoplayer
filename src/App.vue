<template>
    <div id="player" class="col-xs-12 col-sm-12 col-lg-4 col-lg-offset-4">
      <div class="row">
        <div class="jumbotron">
          <video-player :options="videoOptions" @player-state-changed="playerStateChanged"></video-player>

        </div>
        <div class="panel panel-primary">
          <div class="panel-heading">
            <h3 class="panel-title">Logs</h3>
          </div>
          <div class="panel-body">
            <ul>
              <span v-if="logs.length == 0"> video not started yet</span>
              <li v-for="log in logs">
                {{ log }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>


</template>

<script>
import Vue from 'vue'
import VideoPlayer from 'vue-video-player'
VideoPlayer.config({
  youtube: true,  // default false（youtube的支持）
  // switcher: true, // default true（播放源切换功能）
  // hls: true       // default true（直播功能的支持）
})
Vue.use(VideoPlayer)

export default {
  name: 'player',
  // props: ['logs'],
  data() {
    return {
      logs: [],
      playing: false,
      last_timestamp: null,
      seeked: false,
      videoOptions: {
        source: {
          type: "video/youtube",
          src: "https://www.youtube.com/watch?v=iD_MyDbP_ZE"
        },
        defaultSrcReId: "low",
        techOrder: ["youtube"],
        autoplay: false,
        controls: false,
        ytControls: true
      }
    }
  },
  methods:{
    playerStateChanged(e){

      if (this.last_timestamp == null && e.currentTime != undefined ){
        this.last_timestamp = e.currentTime;
      }
      if (e.currentTime != undefined && (e.currentTime - this.last_timestamp) > 0.3 ){
        this.logs.push('Seeked forward');
        this.seeked = true
      }
      if( e.currentTime == 0){
        this.logs.push("Video is Ready");
        this.playing = true;
      }
      if (e.play){
        if(this.seeked){
          this.seeked = false;
        }else{
          this.logs.push("Video is Playing");
          this.playing = true;
        }
      }
      if (e.pause) {
        if(this.seeked){}
        else{
          this.play = false;
          this.logs.push("Video is Paused");
        }
      }

      this.last_timestamp = e.currentTime;
    }
  }
}

</script>

<style>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
