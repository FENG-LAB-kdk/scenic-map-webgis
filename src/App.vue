<template>
  <div style="display: flex; height: 100vh; width: 100vw; overflow: hidden">
    <!-- 左侧边栏 -->
    <div
      class="sidebar"
      style="width: 300px; background: #f5f5f5; overflow-y: auto; padding: 10px"
    >
      <h2 style="padding: 0 0 10px 0">景点列表</h2>
      <ul style="list-style: none; padding: 0">
        <li
          v-for="spot in spots"
          :key="spot.id"
          @click="flyToSpot(spot)"
          :class="{ active: selectedId === spot.id }"
          style="
            padding: 10px;
            cursor: pointer;
            border-bottom: 1px solid #ddd;
            display: flex;
            justify-content: space-between;
            align-items: center;
          "
        >
          <div>
            <strong>{{ spot.name }}</strong>
            <p style="font-size: 0.9em; color: #666; margin: 0">
              {{ spot.desc }}
            </p>
          </div>
          <button
            @click.stop="deleteSpot(spot.id)"
            style="
              background: #e74c3c;
              color: white;
              border: none;
              border-radius: 4px;
              padding: 4px 8px;
              cursor: pointer;
            "
          >
            删除
          </button>
        </li>
      </ul>
    </div>
    <!-- 右侧地图 -->
    <div style="flex: 1; display: flex; overflow: hidden">
      <MapView
        ref="mapViewRef"
        :spots="spots"
        style="height: 100%; width: 100%"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import MapView from "./components/MapView.vue";

// 1. 默认数据（只在第一次使用时生效）
const defaultSpots = [
  {
    id: 1,
    name: "大雁塔",
    lng: 108.959,
    lat: 34.219,
    desc: "唐代古塔，音乐喷泉",
  },
  { id: 2, name: "钟楼", lng: 108.94, lat: 34.261, desc: "市中心标志性建筑" },
  { id: 3, name: "兵马俑", lng: 109.273, lat: 34.385, desc: "世界第八大奇迹" },
];

// 2. 从 localStorage 读取，若没有则使用默认数据
const stored = localStorage.getItem("scenicSpots");
const spots = ref(stored ? JSON.parse(stored) : defaultSpots);

// 3. 自动保存：每次 spots 变化时存入 localStorage
watch(
  spots,
  (newVal) => {
    localStorage.setItem("scenicSpots", JSON.stringify(newVal));
  },
  { deep: true },
);

// 4. 地图组件引用
const mapViewRef = ref(null);
const selectedId = ref(null);

// 5. 点击列表项飞行动画
const flyToSpot = (spot) => {
  selectedId.value = spot.id;
  if (mapViewRef.value) {
    mapViewRef.value.flyTo(spot);
  }
};

// 6. 删除景点
const deleteSpot = (id) => {
  spots.value = spots.value.filter((spot) => spot.id !== id);
  if (selectedId.value === id) {
    selectedId.value = null;
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
  font-family: "Segoe UI", sans-serif;
  overflow: hidden;
}
.active {
  background-color: #b3d4fc;
}
</style>
