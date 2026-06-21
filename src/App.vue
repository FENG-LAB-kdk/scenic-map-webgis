<template>
  <div style="display: flex; height: 100vh; width: 100vw; overflow: hidden;">
    <div class="sidebar" style="width: 300px; background: #f5f5f5; overflow-y: auto;">
      <h2 style="padding: 10px;">景点列表</h2>
      <ul style="list-style: none; padding: 0;">
        <li v-for="spot in spots" :key="spot.id"
            @click="flyToSpot(spot)"
            :class="{ active: selectedId === spot.id }"
            style="padding: 10px; cursor: pointer; border-bottom: 1px solid #ddd;">
          <strong>{{ spot.name }}</strong>
          <p style="font-size: 0.9em; color: #666;">{{ spot.desc }}</p>
        </li>
      </ul>
    </div>
    <div style="flex: 1; display: flex; overflow: hidden;">
      <MapView ref="mapViewRef" :spots="spots" style="height: 100%; width: 100%;" />
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import MapView from './components/MapView.vue';

const spots = ref([
  { id: 1, name: '大雁塔', lng: 108.959, lat: 34.219, desc: '唐代古塔，音乐喷泉' },
  { id: 2, name: '钟楼', lng: 108.940, lat: 34.261, desc: '市中心标志性建筑' },
  { id: 3, name: '兵马俑', lng: 109.273, lat: 34.385, desc: '世界第八大奇迹' },
]);

const mapViewRef = ref(null);
const selectedId = ref(null);

const flyToSpot = (spot) => {
  selectedId.value = spot.id;
  if (mapViewRef.value) {
    mapViewRef.value.flyTo(spot);
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: 'Segoe UI', sans-serif;
  overflow: hidden;
}
.active {
  background-color: #b3d4fc;
}
</style>