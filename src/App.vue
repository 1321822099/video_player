<template>
  <div class="video-player">
    <video ref="videoRef" class="video-player__video" @timeupdate="updateProgress">
      <source :src="'./test.mp4'" type="video/mp4" />
    </video>

    <div class="video-player__controls">
      <button class="video-player__control-btn" @click="togglePlay">
        {{ state.isPlaying ? "Pause" : "Play" }}
      </button>

      <div class="video-player__progress-bar">
        <div class="video-player__progress" :style="{ width: state.progress + '%' }"></div>
      </div>

      <div class="video-player__volume-bar">
        <button class="video-player__control-btn" @click="toggleVolume">
          Volume
        </button>
        <input type="range" v-if="state.isVolumeVisible" v-model="state.volume" min="0" max="1" step="0.1" />
      </div>
    </div>

    <div class="video-player__danmu-container">
      <div v-for="(danmu, index) in danmuList" :key="index" class="video-player__danmu"
        :style="{ top: `${danmu.top}px`, color: '#fff', right: `${danmu.right}px` }">
        <span :style="{
          background:danmu.color,
          display:'inline-block',
          padding:'5px'
        }">
          {{ danmu.text }}
        </span>
      </div>
    </div>

    <input class="video-player__danmu-input" v-model="state.danmuText" placeholder="Enter danmu text"
      @keyup.enter="addDanmu" />
  </div>
</template>

<script>
import { reactive, ref } from 'vue';

export default {
  name: 'VideoPlayer',
  props: {
    videoUrl: {
      type: String,
      required: true,
    },
  },
  setup() {
    const state = reactive({
      isPlaying: false,
      progress: 0,
      isVolumeVisible: false,
      volume: 0.5,
      danmuText: '',
    });

    const videoRef = ref(null);
    const danmuList = reactive([]);

    const togglePlay = () => {
      state.isPlaying = !state.isPlaying;
      if (state.isPlaying) {
        videoRef.value.play();
      } else {
        videoRef.value.pause();
      }
    };

    const toggleVolume = () => {
      state.isVolumeVisible = !state.isVolumeVisible;
    };

    const updateProgress = () => {
      const video = videoRef.value;
      const progress = (video.currentTime / video.duration) * 100;
      state.progress = Math.floor(progress);
    };

    const addDanmu = () => {
      if (state.danmuText.trim() !== '') {
        const danmu = {
          text: state.danmuText,
          top: Math.floor(Math.random() * 160) + 20, // Random top position between 20 and 180
          color: getRandomColor(),
          right: 0, // Starting position from the right
        };
        danmuList.push(danmu);
        state.danmuText = '';
      }
    };

    const getRandomColor = () => {
      var color = "#";
      for (var i = 0; i < 6; i++) color += parseInt(Math.random() * 16).toString(16);
      return color;
    };

    const moveDanmus = () => {
      for (let i = 0; i < danmuList.length; i++) {
        const danmu = danmuList[i];
        danmu.right += 2; // Adjust the speed of the danmu here
        if (danmu.right > window.innerWidth) {
          danmu.right = -200; // Reset the position if danmu goes beyond the window
        }
      }
    };

    // Call moveDanmus every 16 milliseconds (adjust the interval for smoother animation if needed)
    setInterval(moveDanmus, 16);

    return {
      state,
      videoRef,
      danmuList,
      togglePlay,
      toggleVolume,
      updateProgress,
      addDanmu,
      getRandomColor,
    };
  },
};
</script>

<style scoped>
.video-player {
  position: relative;
  width: 800px;
  height: 450px;
}

.video-player__video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.video-player__controls {
  position: absolute;
  bottom: 20px;
  left: 0;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.video-player__control-btn {
  margin: 0 10px;
}

.video-player__progress-bar {
  width: 60%;
  height: 5px;
  background-color: #ccc;
}

.video-player__progress {
  height: 100%;
  background-color: #f00;
}

.video-player__volume-bar {
  margin-left: 20px;
  position: relative;
}

.video-player__danmu-container {
  position: absolute;
  top: 20px;
  left: 0;
  width: 100%;
  height: 80%;
  overflow: hidden;
}

.video-player__danmu {
  position: absolute;
  white-space: nowrap;
}

.video-player__danmu-input {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
}
</style>