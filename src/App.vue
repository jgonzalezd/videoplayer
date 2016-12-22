<template>
    <div id="player" class="col-xs-12 col-sm-12 col-lg-4 col-lg-offset-4">
      <div class="row">
        <div class="jumbotron">
          <video-player :options="videoOptions" :configs="{ youtube: true }" @player-state-changed="playerStateChanged" ref="myPlayer"></video-player>
          <p> Duration: {{ duration }} </p>
        </div>
        <div class="panel panel-primary">
          <div class="panel-heading">
            <h3 class="panel-title">Logs</h3>
          </div>
          <div class="panel-body">
            <ul>
              <span v-if="logs.length == 0"> video not started yet</span>
              <li v-if="readyState">Video is Ready</li>
              <li v-for="log in logs">
                {{ log }}
              </li>
              <li v-if="hasEnded">Video has ended</li>
            </ul>
          </div>
        </div>
      </div>
    </div>


</template>

<script>
import Vue from 'vue'
import VideoPlayer from 'vue-video-player'
Vue.use(VideoPlayer)

export default {
  name: 'player',
  // props: ['logs'],
  data() {
    return {
      logs: [],
      playing: false,
      readyState: false,
      last_timestamp: null,
      previousSrc: null,
      isFullscreen: false,
      videoDuration: 0,
      hasEnded: false,
      videoOptions: {
        source: [
          { type: "video/youtube", src: "https://www.youtube.com/watch?v=Je2WLIUQVnY", res: 1 },
          { type: "video/youtube", src: "https://www.youtube.com/watch?v=ps7coAgGswY", res: 2 }
        ],
        techOrder: ["youtube"],
        autoplay: true,
        controls: false,
        ytControls: true
      }
    }
  },
  mounted(){
    this.readyState = this.player.readyState
  },
  computed: {
    player() {
      return this.$refs.myPlayer.player
    },
    duration(){
      var date = new Date(null);
      date.setSeconds(this.videoDuration); // specify value for SECONDS here
      return date.toISOString().substr(11, 8);
    }
  },

  methods:{
    playerStateChanged(e){
      // We monitor the duration of current video
      this.videoDuration = this.player.duration()

      // Fullscreen function is not working
      // if(!!this.player.isFullscreen()) this.logs.push('Entered Full Screen');

      // Video starts to play
      if (this.last_timestamp == null && e.currentTime != undefined ){
        this.readyState = true
        this.last_timestamp = e.currentTime;
        this.previousSrc = this.player.currentSrc()
      }

      //If src has changed
      if((this.player.currentSrc() != this.previousSrc) && !!this.previousSrc){
        this.logs.push('Source has changed');
        this.previousSrc = this.player.currentSrc()
      }

      //Set current video at last timestamp. Wont autoplay if embed in past if
      if(e.currentTime == 0 && this.last_timestamp != 0) this.player.currentTime(this.last_timestamp)

      //We detect a seeking action
      if (e.currentTime != undefined && (e.currentTime - this.last_timestamp) > 0.9 ){
        this.logs.push('Seeked forward');
        console.log("seeked forward")
        this.seeked = true;
        let vo;
        vo = this.videoOptions;
        vo.source.reverse();
        vo['start'] = e.currentTime;
        vo['seeking'] = true
        this.videoOptions = vo;
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
        if(!this.seeked){
          this.play = false;
          this.logs.push("Video is Paused");
        }
      }

      if(this.player.ended()) this.hasEnded = true

      if (!!e.currentTime) this.last_timestamp = e.currentTime;
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
