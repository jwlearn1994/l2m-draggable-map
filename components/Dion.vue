<template>
  <div class="section-dion">
    Dion
    <div @click="$emit('back')">Back</div>
  </div>
</template>

<script>
export default {
  name: 'Dion',
};
</script>

<style lang="scss">
.section-dion {
  position: absolute;
  left: 0;
  top: 0;
}
</style>
