<script setup>
import { reactive } from 'vue';
//定義SearchInput.vue組件要觸發的所有"事件",defProps是定義屬性
const emit = defineEmits(['place-data'])
// searchTerm 用reactive函式(響應式對象)來隨時更新使用者輸入的國家
const searchTerm = reactive({
    query:'',//存放使用者輸入的國家
    timeout:null,//延遲響應式狀態
    result:null,//從weaatherAPI fetch到的資料

})
//handelSearch:處理抓到的資料,並顯示搜尋的國家結果
const handleSearch = () =>{
    // 由於每次在input 文字時有延遲,不會每輸入一次,就觸發handleSearch(),造成錯誤
    clearTimeout(searchTerm.timeout)
    searchTerm.timeout = setTimeout(async () => {
        //如果我timeout延遲後,有抓到結果
        if (searchTerm.query !== ''){
            //要把url中預設的地方名稱換成${物件.屬性}
            const res = await fetch(`http://api.weatherapi.com/v1/search.json?key=1d3544dad8ef4f0db0a120049240702&q=${searchTerm.query}`)
            // console.log(searchTerm.query)
            //res用來存放fetch到來自weather API回傳的response
            //data用來存取res中的內容
            const data = await res.json()
            // console.log(data)=>測試
            searchTerm.result = data
          //  console.log(searchTerm.result)
        }else{
            //當我刪除輸入內容時,將會清空搜尋所引到的結果
            searchTerm.result = null
        }
    }, 500)

}

//button:searchTerm抓到資料顯示後,點選我想要的資料,並取得資料的其他內容
const getWeather = async (id) =>{
    //要把url中預設的地方名稱換成${參數列表中的屬性}
    const res = await fetch(`http://api.weatherapi.com/v1/forecast.json?key=1d3544dad8ef4f0db0a120049240702&q=id:${id}&days=7&aqi=no&alerts=no`)
    //將res中fetch到的東西,轉換成JSON,就可以開始取資料,這步驟很重要
    const data = await res.json()
    // 觸發place-data,傳遞data給根組件,讓其他組件也可以使用data的內容
    emit('place-data',data)
    // console.log(data)
    //擷取到之後並再次點即後,就把搜尋清空,回到初始狀態,以便在搜尋下一個
    searchTerm.query = ''
    searchTerm.result = null
}
</script>

<template>
  <div>
    <!-- 搜尋欄位 -->
    <form>
      <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
        <i class="fa-solid fa-magnifying-glass p-2 text-indigo-600"></i>
        <input
          type="text"
          placeholder="Search for a place"
          class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
          v-model = "searchTerm.query"
          @input = "handleSearch"
        />
      </div>
    </form>
    <!-- 列出找到的項目 -->
    <div class="bg-white my-2 rounded-lg shadow-lg">
        <div v-if="searchTerm.result !== null">
            <div v-for="place in searchTerm.result" :key="place.id">
            <!-- 用for迴圈直接取得result的資料,並根據json回傳的id取出 -->
            <button @click="getWeather(place.id)" class="px-3 my-2 hover:text-indigo-600 hover:font-bold w-full text-left">
                {{place.name}},{{place.region}},{{place.country}}
            </button>
        </div>
    </div>
    </div>
  </div>
</template>
<!-- weather API key : 1d3544dad8ef4f0db0a120049240702 -->
<!-- temp.json的資料,是來自weather API中,forecast的資料,避免之後發生資料錯誤,或是我們需要再取到其他資料時,可以參考 -->
<!-- 
載入reactive套件,將SearchTerm這個常數物件設定為響應式對象
form:

button:
-->