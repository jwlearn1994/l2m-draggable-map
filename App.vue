<template>
  <div id="app">
    <dragger
      class="movie-container"
      ref="movie-container"
      :draggable="nowSection !== 'WorldMap'"
      map
    >
      <!-- pc -->
      <div class="movie-wrap" v-if="!isMobile">
        <!-- worldmap -->
        <template v-show="nowSection === 'WorldMap'">
          <video
            v-show="worldIntro"
            autoplay
            muted
            ref="movie-intro"
            src="https://vod.plaync.com/Lineage2M/2019.09_preorder/worldmap-intro-1016.mp4"
            type="video/mp4"
            @ended="worldIntro = false"
          ></video>
          <video
            v-show="!worldIntro"
            ref="movie-worldmap"
            autoplay
            muted
            loop
            type="video/mp4"
            src="https://vod.plaync.com/Lineage2M/2019.09_preorder/worldmap-loop-1016.mp4"
          ></video>
        </template>
        <!-- maps -->
        <template v-show="nowSection !== 'WorldMap'">
          <video
            v-show="entrying"
            ref="movie-entrying"
            autoplay
            muted
            type="video/mp4"
            :src="entrySrc"
            @ended="onEntryEnded"
          ></video>
          <video
            v-show="!entrying"
            ref="movie-area"
            autoplay
            muted
            loop
            type="video/mp4"
            :src="areaSrc"
          ></video>
        </template>
      </div>
      <!-- mobile -->
      <div class="movie-wrap mobile" v-else>
        <video
          v-show="nowSection !== 'WorldMap'"
          ref="movie-entrying"
          autoplay
          muted
          playsinline
          type="video/mp4"
          :src="entrySrc"
          @ended="onEntryEnded"
        ></video>
      </div>
    </dragger>
    <!-- Sections -->
    <component
      :is="nowSection"
      @open="onOpenMap"
      @back="onBack"
    />
  </div>
</template>

<script>
import Dragger from './components/Dragger.vue';
import WorldMap from './components/WorldMap.vue';
import Gludio from './components/Gludio.vue';
import Dion from './components/Dion.vue';

const u = navigator.userAgent;

const isMobile = {
  Android: function() {
    return (/Android/i).test(u);
  },
  BlackBerry: function() {
    return (/BlackBerry/i).test(u);
  },
  iOS: function() {
    return (/iPhone|iPad|iPod/i).test(u);
  },
  Opera: function() {
    return (/Opera Mini/i).test(u);
  },
  Windows: function() {
    return (/IEMobile/i).test(u);
  },
  any: function() {
    return (isMobile.Android() || isMobile.BlackBerry() || isMobile.iOS() || isMobile.Opera() || isMobile.Windows());
  }
};

export default {
  name: 'App',
  components: {
    Dragger,
    WorldMap,
    Gludio,
    Dion
  },
  data() {
    return {
      nowSection: 'WorldMap',
      worldIntro: true,
      entrying: false,
      entrySrc: '',
      areaSrc: '',
      isMobile: isMobile.any(),
    };
  },
  methods: {
    onOpenMap(mapName) {
      this.entrySrc = `https://vod.plaync.com/Lineage2M/2019.09_preorder/${mapName.toLowerCase()}-entry${this.isMobile ? '-mobile' : ''}-1016.mp4`;
      this.areaSrc = `https://vod.plaync.com/Lineage2M/2019.09_preorder/${mapName.toLowerCase()}-loop-1016.mp4`;
      this.entrying = true;
      this.nowSection = mapName;
    },
    onEntryEnded() {
      this.entrying = false;
    },
    onBack() {
      this.nowSection = 'WorldMap';
      this.entrySrc = '';
      this.areaSrc = '';
      this.$refs['movie-container'].reset();
    },
  },
};
</script>

<style lang="scss">
html, body {
  height: 100%;
  width: 100%;
}

body {
  margin: 0;
  background: #000;
}

img {
  display: block;
  max-width: 100%;
}

#app {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  font-size: 16px;
  overflow: hidden;
  .movie-container {
    width: 3316px;
    transition: .5s;
    overflow: hidden;
    @media (max-width: 768px) {
      left: -580px;
    }
  }
}

.movie-wrap {
  position: relative;
  width: 3316px;
  height: 2160px;
  background: url('https://image-cdn.plaync.com.tw/nct/lineage2m/gameinfo/map/img/worldmap_bg_mobile.jpg') 50% 50% / cover no-repeat;
  video {
    position: absolute;
    left: 0;
    top: 0;
    margin: auto;
    width: 100%;
  }
  @media (max-width: 768px) {
    width: 1688px;
    height: 1100px;
  }
}
</style>
