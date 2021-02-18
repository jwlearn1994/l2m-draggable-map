<template>
  <div class="section-gludio">
    Gludio
    <div @click="$emit('back')">Back</div>
  </div>
</template>

<script>
export default {
  name: 'Gludio',
};
</script>

<style lang="scss">
.section-gludio {
  position: absolute;
  left: 0;
  top: 0;
}
</style>
