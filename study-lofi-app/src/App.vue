<template>
  <div class="app-container">
    <video
    ref="videoPlayer"
      class="video-background"
      :src="currentScene"
      muted=1
      loop
      autoplay></video>

    <SceneSelector 
    :scenes="sceneList"
      :selectedScene="currentScene"
      @scene-selected="setScene"></SceneSelector>
      <button @click="startAudio">Start Audio</button>
  </div>
</template>

<script>
import SceneSelector from './components/SceneSelector.vue';
import song1 from '../src/assets/lofi-royalty-free-songs/Austin-Hughes-Distant-Memories.mp3';
import song2 from '../src/assets/lofi-royalty-free-songs/Austin-Hughes-Starfish.mp3';
import song3 from '../src/assets/lofi-royalty-free-songs/Bensky-Lu-Old-Feelings.mp3';
import song4 from '../src/assets/lofi-royalty-free-songs/Bensky-Lu-Right-Here-Right-Now.mp3';
import song5 from '../src/assets/lofi-royalty-free-songs/Bensky-Lu-Searching-For-You.mp3';
import scene1 from '../src/assets/scenes/bedroom.mp4';
import scene2 from '../src/assets/scenes/coffee-shop.mp4';
import scene3 from '../src/assets/scenes/library.mp4';

export default {
  components: {
    SceneSelector
  },
  data() {
    return {
      sceneList: [scene1, scene2, scene3],
      currentScene: scene1, // Initial scene
      audioFiles: [
        song1, song2, song3, song4, song5
      ],
      audioQueue: [],  // Queue of audio file indexes
      currentAudioIndex: 0,
      audioObject: null, // Store the audio object
      audioStarted: false,
    };
  },
  mounted() {
    this.generateAudioQueue();
  },
  methods: {
    setScene(scene) {
      this.currentScene = scene;
      this.$refs.videoPlayer.load(); // Reload the video element
    },

    startAudio() {
      if (!this.audioStarted) {
        this.playNextAudio();
        this.audioStarted = true;
      }
    },

    playNextAudio() {
      if (this.audioQueue.length === 0) {
        this.generateAudioQueue(); // Regenerate if queue is empty
      }

      const audioIndex = this.audioQueue.shift(); // Get the next audio index
      this.currentAudioIndex = audioIndex;

      this.audioObject = new Audio(this.audioFiles[audioIndex]); // Create a new audio object each time

      this.audioObject.addEventListener('ended', () => {
        this.playNextAudio();
      });

      this.audioObject.play(); // Moved here to after the button click.
    },

    generateAudioQueue() {
      const indexes = Array.from({ length: this.audioFiles.length }, (_, i) => i); // Array of indexes

      // Shuffle the indexes
      for (let i = indexes.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [indexes[i], indexes[j]] = [indexes[j], indexes[i]];
      }

      this.audioQueue = indexes;
    }
  }
};
</script>

<style>
body, html {
  margin: 0;
  padding: 0;
  overflow: hidden; /* Hide scrollbars */
  height: 100%;
}

.app-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  position: relative; /* Required for absolute positioning of SceneSelector */
}

.video-background {
  width: 100%;
  height: 100%;
  object-fit: fill; /* Cover the entire viewport */
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1; /* Ensure video is behind the buttons */
  pointer-events: none; /* Prevent video from interfering with button clicks */
}
</style>