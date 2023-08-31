<script setup>
import { ref, computed, watch } from 'vue';

// 修改這份 YouBike 即時資訊表，並加上
// 1. 站點名稱搜尋 V
// 2. 目前可用車輛 / 總停車格 的排序功能 V
// 3. 加入分頁功能，每頁 20 筆資料

// 欄位說明:
// https://data.taipei/dataset/detail?id=c6bc8aed-557d-41d5-bfb1-8da24f78f2fb
// sno：站點代號、 sna：場站名稱(中文)、 tot：場站總停車格、
// sbi：場站目前車輛數量、 sarea：場站區域(中文)、 mday：資料更新時間、
// lat：緯度、 lng：經度、 ar：地(中文)、 sareaen：場站區域(英文)、
// snaen：場站名稱(英文)、 aren：地址(英文)、 bemp：空位數量、 act：全站禁用狀態

const uBikeStops = ref([]);
const inputName = ref('');
const sbiAsc = ref(false);
const totAsc = ref(false);
//暫存總頁數
let datas = ref(0);

//分頁功能
const perpage = 20; //顯示資料筆數
const currentPage = ref(1); //記錄當下頁數

fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
  });

// 搜尋排序
const filterStops = computed({
  get: () => {
    if(inputName.value) {
      datas.value = uBikeStops.value.filter(item => item.sna.match(inputName.value)).length;
      return uBikeStops.value.filter(item => item.sna.match(inputName.value)).slice(pageStart.value,pageEnd.value);
    }else{
      datas.value = uBikeStops.value.length;
      return uBikeStops.value.slice(pageStart.value,pageEnd.value);
    }
  },
  set: (val) => {
    return val;
  }
  }
)

//處理指定排序
function sortType(type){
  if(type == 'sbi'){
    sbiAsc.value = !sbiAsc.value;
    let sbiSort = ref(sbiAsc.value ? 
    filterStops.value.sort((x,y) => x.sbi > y.sbi ? 1:-1) 
    : filterStops.value.sort((x,y) => x.sbi < y.sbi ? 1:-1))
    filterStops.value = sbiSort.value;
  }
  if(type == 'tot'){
    totAsc.value = !totAsc.value;
    let totSort = ref(totAsc.value ? 
    filterStops.value.sort((x,y) => x.tot > y.tot ? 1:-1) 
    : filterStops.value.sort((x,y) => x.tot < y.tot ? 1:-1));
    filterStops.value = totSort.value;
  }
}


//總頁數
const totalPage = computed(()=>Math.ceil(datas.value / perpage));
//該頁第一個index值
const pageStart = computed(()=>(currentPage.value - 1)* perpage); 
//該頁最後index值
const pageEnd = computed(()=> currentPage.value * perpage);
const setPage = (page) => {
  if(page <= 0 || page > totalPage.value){
    return ;
  }else{
    currentPage.value = page;
  }
}

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};


</script>

<template>
<div class="app">
  <p>
    站點名稱搜尋: <input type="text" v-model="inputName">
  </p>
  <p>{{ totalPage}} {{ pageStart }} {{ pageEnd  }} {{ currentPage }}</p>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th>目前可用車輛
          <a href=""  @click.prevent="sortType('sbi')">
          <i class="fa fa-sort-asc"  v-if="sbiAsc == false" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" v-if="sbiAsc == true" aria-hidden="true"></i>
          </a>
        </th>
        <th>總停車格
          <a href=""  @click.prevent="sortType('tot')">
          <i class="fa fa-sort-asc"  v-if="totAsc == false" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" v-if="totAsc == true" aria-hidden="true"></i>
          </a>
        </th>
        <th>資料更新時間</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="s in filterStops" :key="s.sno">
        <td>{{ s.sno }}</td>
        <td>{{ s.sna }}</td>
        <td>{{ s.sarea }}</td>
        <td>{{ s.sbi }}</td>
        <td>{{ s.tot }}</td>
        <td>{{ timeFormat(s.mday) }}</td>
      </tr>
    </tbody>
  </table>
  <div class="d-flex gap-8">
  <ul class="pagination">
    <li>
      <a href="" @click.prevent="setPage(currentPage-1)">
        <i class="fa fa-arrow-left"  :class="{ 'text-gray-300':(currentPage === 1) }">
        </i>
      </a>
    </li>

    <li v-for="(n,i) in totalPage" :key="i">
      <a href="" 
      :class="{ 'text-gray-300':(currentPage == n) }" 
      @click.prevent="setPage(n)">{{ n }}</a>
    </li>

    <li>
      <a href="" :class="{ 'text-gray-300':(currentPage === totalPage) }" @click.prevent="setPage(currentPage+1)">
        <i class="fa fa-arrow-right">
        </i>
      </a>
    </li>
  </ul>
  </div>
</div>
</template>

<style scoped>
.app {
  padding: 1rem;
}
.text-gray-300{
  color: gray;
  text-decoration: none;
  cursor:default;
}
.d-flex{
  display: flex;
  justify-content: center;
}
.pagination{
  display: flex;
  gap:8px;
}
</style>