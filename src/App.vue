<script setup>
import {ref} from "vue";
import SearchInput from './components/SearchInput.vue';
import WeatherCard from './components/WeartherCard.vue';
// 用來接收SearchInput中getweather()抓到的資料,存到陣列中
const places = ref([]);
const addPlace = (data)=>{
  // console.log(data)
  //ref 要取到值,要加上value,和reactive不一樣
  places.value.push(data)
};
const deletePlace = (name) =>{
  // console.log(name)
  //confirm()很好用,會以alert.()跳出confirm裡的內容,當我執行的結果是true時就會執行if裡的程式,false會跳回去,
  if(confirm('Are you sure?')){
    // 移除我remove-place => delete-place => deletePlace中對應的地點,如果我一次搜尋多個地點時,才不會錯誤
  places.value = places.value.filter(p => p.location.name !==name)
  }
}
</script>
<template>
  <main>
    <!-- 日期 -->
    <div class="text-center mb-6">
      {{ new Date().toLocaleDateString('zh-Hant',{
        weekday:'long',
        year:'numeric',
        month:'long',
        day:'numeric',
      })}}
    </div>
    <!-- 搜尋 -->
    <div>
      <SearchInput @place-data="addPlace"/>
    </div>
    <!-- Weather Card-->
    <div class="grid- grid-cols-2 gap-4">
      <!-- 以index去抓到place -->
      <div v-for="(place,idx) in places" :key="idx">
        <!-- 接收子組件WeatherCard的子組件(deletep-place)WeatherInfo傳遞的remove-place
        ,在觸發常數deletePlace -->
        <WeatherCard :place="place" @delete-place="deletePlace"/>
      </div>
    </div>
  </main>
</template>
<style scoped></style>


<!--
  Date:
  1.用關鍵字New創建一個對象使用Date函式
  2.針對這個對象使用格式化日期函數,設定語言和格式參數,設定為long是要以"完整的文本形式"呈現
  3.已經有載入BootstrapCDN就可以直接用他的class語法
-->