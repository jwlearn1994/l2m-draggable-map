<template>
  <div class="section-worldmap">
    <div class="map-list">
      <div class="map-gludio" @click="$emit('open', 'Gludio')">
        <span class="light"></span>
        <div />
      </div>
      <div class="map-dion" @click="$emit('open', 'Dion')">
        <span class="light"></span>
        <div />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'WorldMap',
};
</script>

<style lang="scss">
.section-worldmap {
  position: absolute;
  width: 3316px;
  height: 2160px;
  color: #fff;
  font-size: 50px;
  @media (max-width: 768px) {
    width: 1688px;
    height: 1100px;
  }
  .map-list {
    [class^='map-'] {
      position: absolute;
      width: 254px;
      height: 254px;
      overflow: hidden;
      cursor: pointer;
      @media (max-width: 768px) {
        width: 127px;
        height: 127px;
      }
      .light {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-size: 254px auto;
        background-image: url('https://image-cdn.plaync.com.tw/nct/lineage2m/gameinfo/map/img/worldmap_list_blur_active.png');
        @media (max-width: 768px) {
          background-size: 127px auto;
        }
      }
      > div {
        position: absolute;
        width: 100%;
        height: 116px;
        top: 50%;
        left: 0;
        background-position: 50% 0;
        background-size: auto 116px;
        background-repeat: no-repeat;
        background-color: transparent;
        transform: translateY(-50%);
        @media (max-width: 768px) {
          height: 66px;
          background-size: auto 66px;
        }
      }
      &.map-gludio {
        top: 1130px;
        left: 1125px;
        @media (max-width: 768px) {
          top: 570px;
          left: 650px;
        }
        > div {
          background-image: url('https://image-cdn.plaync.com.tw/nct/lineage2m/gameinfo/map/img/gludio_list_active_pc.png');
        }
      }
      &.map-dion {
        top: 1070px;
        left: 1330px;
        @media (max-width: 768px) {
          top: 550px;
          left: 750px;
        }
        > div {
          background-image: url('https://image-cdn.plaync.com.tw/nct/lineage2m/gameinfo/map/img/dion_list_active_pc.png');
        }
      }
    }
  }
}
</style>
