<template>
  <!-- 外层容器确保高度 100% -->
  <div class="map-wrapper" style="height: 100%; width: 100%">
    <div id="map" style="height: 100%; width: 100%"></div>
  </div>
</template>

<script setup>
import { onMounted, onUnmounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import markerIcon from "leaflet/dist/images/marker-icon.png";
import markerShadow from "leaflet/dist/images/marker-shadow.png";

// 接收父组件传入的景点数据
const props = defineProps({
  spots: {
    type: Array,
    required: true,
  },
});

let map = null;
let markers = [];

// 暴露给父组件的方法
defineExpose({
  flyTo(spot) {
    if (map) {
      map.flyTo([spot.lat, spot.lng], 14);
      const marker = markers.find((m) => m.options.id === spot.id);
      if (marker) {
        marker.openPopup();
      }
    }
  },
  // 额外暴露刷新地图方法，方便调试
  refreshMap() {
    if (map) {
      map.invalidateSize();
    }
  },
});

// 添加所有标记
function addMarkers() {
  if (!map) return;
  markers.forEach((m) => map.removeLayer(m));
  markers = [];
  props.spots.forEach((spot) => {
    const marker = L.marker([spot.lat, spot.lng])
      .bindPopup(`<b>${spot.name}</b><br>${spot.desc}`)
      .addTo(map);
    marker.options.id = spot.id;
    markers.push(marker);
  });
}

// 窗口大小改变时，强制地图重新计算尺寸
function handleResize() {
  if (map) {
    // 使用 setTimeout 确保浏览器完成重排后再刷新
    setTimeout(() => {
      map.invalidateSize();
    }, 50);
  }
}

onMounted(() => {
  // 修复 Leaflet 默认图标路径
  L.Marker.prototype.options.icon = L.icon({
    iconUrl: markerIcon,
    shadowUrl: markerShadow,
    iconSize: [25, 41],
    iconAnchor: [12, 41],
  });

  // 创建地图实例
  map = L.map("map").setView([34.34, 108.94], 10);

  // 使用 OpenStreetMap 瓦片（国内可用性较好）
  L.tileLayer(
    "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
    {
      attribution: "高德地图",
    },
  ).addTo(map);

  // 添加标记
  addMarkers();

  // 监听窗口 resize 事件
  window.addEventListener("resize", handleResize);

  // 额外：在下一帧再刷新一次，确保布局稳定
  setTimeout(() => {
    if (map) map.invalidateSize();
  }, 200);
});

// 组件销毁时移除事件监听
onUnmounted(() => {
  window.removeEventListener("resize", handleResize);
  if (map) {
    map.remove();
    map = null;
  }
});
</script>

<style scoped>
.map-wrapper,
#map {
  height: 100%;
  width: 100%;
}
</style>
