<script setup>
import BorderLine from './BorderLine.vue'
import WeatherForcastDay from './WeatherForcastDay.vue'
import WeatherInfo from './WeatherInfo.vue'
import {ref} from 'vue'
defineProps({
    place:Object
})
//預設false,不會顯示天氣細節
const showDetail=ref(false)
//處理顯示多個天氣視窗時,移除其中一個地點時,上一個會記錄被刪除的地點的showDetail狀態
const emit = defineEmits(['delete-place'])
//placeName接收天氣資訊中remove-place傳進來的地點
const removePlace=(placeName) => {
  emit('delete-place',placeName)
  showDetail.value=false} 
</script>

<template>
    <!-- 根據temp.json中,從weatherAPI擷取的內容,來觀察並取到我們要的數值 -->
    <!-- is_day ===1就是白天,來自searchInput fetch到的資料,可以在temp.json檢視 -->
  <div :class="place.current.is_day===1 ? 'bg-day':'bg-night' "
  class="text-white p-10 rounded-lg shadow-lg gap-6 mb-6 relative overflow-hidden">
    <!-- 地點和時間 -->
    <div class="mb-2 flex justify-between items-center">
      <div class="flex items-center justify-center gap-2">
        <i class="fa-solid fa-location-dot"></i>
        <h1 class="text-3xl">{{place.location.name}}</h1>
      </div>
      <div class="flex items-center justify-center gap-2">
        <i class="fa-solid fa-clock"></i>
        <h1 class="text-3xl">{{new Date(place.location.localtime).getHours()}}:{{new Date(place.location.localtime).getMinutes()}}</h1>
      </div>
    </div>

    <!-- 即時天氣 -->
    <div class="text-center flex-1">
      <img :src="place.current.condition.icon" alt="icon" width="200" class="mx-auto -mb-10" />
      <h1 class="text-9xl mb-2 -mr-4">{{Math.round(place.current.temp_c)}}&deg;</h1>
      <p class="text-2xl">{{place.current.condition.text}}</p>
    </div>

    <BorderLine />

    <!-- 預測天氣 -->
    <div v-for="(day,idx) in place.forecast.forecastday" :key="idx">
      <!-- Weather daily forecast component goes here -->
      <WeatherForcastDay :day="day"/>
    </div>

    <!-- 天氣資訊-->
    <!-- 用Transition過度一個淡入淡出的效果 -->
    <Transition name="fade">
      <!-- v-show 當掛載的tag為true就顯示,false就隱藏 -->
      <div v-show="showDetail">
          <!-- 在子組建(WeatherInfo)中關閉天氣細節和傳送子組件要remove現有的地點資訊,傳給vue.app根組件 -->
          <WeatherInfo :place="place" @close-info="showDetail = false" @remove-place="removePlace(place.location.name)"/>
      </div>
    </Transition>

    <!-- 天氣資訊細節 -->
    <div class="flex justify-end items-center gap-1 mt-10">
      <!-- 在子組建中打開天氣細節 -->
      <button @click="showDetail = true">More <i class="fa-solid fa-arrow-right text-sm -mb-px"></i></button>
    </div>
  </div>
</template>

<style scoped>
/* 早晚效果 */
.bg-day {
  background-color: #8ec5fc;
  background-image: linear-gradient(62deg, #8ec5fc 0%, #e0c3fc 100%);
}
.bg-night {
  background-color: #07223d;
  background-image: linear-gradient(62deg, #0a2a4a 0%, #270845 100%);
}
/* 天氣資訊淡入淡出 */
.fade-enter-active,
.fade-leave-active{
  transition:opacity 0.5s ease
}
.fade-enter-from,
.fade-leave-to{
  opacity:0
}


</style>