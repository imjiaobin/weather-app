<script setup>
defineProps({
    day:Object
})
</script>

<template>
  <div class="mb-2 flex items-center justify-between">
    <table class="w-full">
      <tr>
        <!-- 星期幾 -->
        <td class="w-1/3">{{new Date(day.date).toLocaleDateString('en-us',{weekday:'long'})}}</td>

        <!-- 天氣圖 -->
        <td class="w-1/3">
          <img :src="day.day.condition.icon" alt="icon" width="30" class="mx-auto" />
        </td>

        <!-- 最高/低溫 -->
        <td class="w-1/3 text-right">{{Math.round(day.day.maxtemp_c)}}&deg; / {{Math.round(day.day.mintemp_c)}}&deg; </td>
      </tr>
    </table>
  </div>
</template>