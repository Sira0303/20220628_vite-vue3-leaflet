<template>
  <div class="map-test">
    <test-map />
  </div>
</template>

<script>
import TestMap from "./components/TestMap.vue";

export default {
  components: {
    TestMap,
  },
};
</script>

<style>
.map-test {
  height: 500px;
}
</style>
