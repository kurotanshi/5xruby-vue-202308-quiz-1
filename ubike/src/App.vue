<script setup>
import { ref, computed, watch, onBeforeMount } from 'vue';

// 修改這份 YouBike 即時資訊表，並加上
// 1. 站點名稱搜尋
// 2. 目前可用車輛 / 總停車格 的排序功能
// 3. 加入分頁功能，每頁 20 筆資料

// 欄位說明:
// https://data.taipei/dataset/detail?id=c6bc8aed-557d-41d5-bfb1-8da24f78f2fb
// sno：站點代號、 sna：場站名稱(中文)、 tot：場站總停車格、
// sbi：場站目前車輛數量、 sarea：場站區域(中文)、 mday：資料更新時間、
// lat：緯度、 lng：經度、 ar：地(中文)、 sareaen：場站區域(英文)、
// snaen：場站名稱(英文)、 aren：地址(英文)、 bemp：空位數量、 act：全站禁用狀態

const uBikeStops = ref([]);
const uBikeBuffer = ref([]);   // 暫存每頁資料
const uBikeCnt = 0;

fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
    uBikeBuffer.value = JSON.parse(data).slice(0,20);
  });  

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};

const siteFilter = ref('');
const sbiAsc = ref(1);  // 1: 可用車輛 升序, 0: 降序
const totAsc = ref(1);  // 1: 可用車位 升序, 0: 降序
const pagesIndex = ref(0);

// ??? 無效
// uBikeBuffer.value = uBikeStops.value;

const siteResults = computed(() => {
  
  if (siteFilter.value.length === 0) {
    return uBikeBuffer.value;
  } else {
    return uBikeBuffer.value.filter ( item => item.sna.includes(siteFilter.value) )
  }

})

const sortAvailbleCar = () => {

  sbiAsc.value = 1 - sbiAsc.value;

  if (sbiAsc.value === 1) {

    // ??? Google 查到, 不是很懂這 Java Script 的排序原理, 要再想想
    uBikeBuffer.value = uBikeBuffer.value.sort((a, b) => {
      return (a.sbi < b.sbi) ? -1 : (a.sbi > b.sbi) ? 1 : 0;
    })

  } else if (sbiAsc.value === 0) {
    uBikeBuffer.value = uBikeBuffer.value.sort((a, b) => {
      return (a.sbi > b.sbi) ? -1 : (a.sbi < b.sbi) ? 1 : 0;
    })    
  }

}

const sortAvailablePos = () => {

  totAsc.value = 1 - totAsc.value;

  if (totAsc.value === 1) {  
    uBikeBuffer.value = uBikeBuffer.value.sort((a, b) => {
      return (a.tot < b.tot) ? -1 : (a.tot > b.tot) ? 1 : 0;
    })
  } else if (totAsc.value === 0) {
    uBikeBuffer.value = uBikeBuffer.value.sort((a, b) => {
      return (a.tot > b.tot) ? -1 : (a.tot < b.tot) ? 1 : 0;
    })    
  }

}

const updatePage = (n) => {
  console.log(n);
  uBikeBuffer.value = uBikeStops.value.slice((n-1)*20 + 0, (n-1)*20 + 20);
}

const addPagesIndex = () => {
  
  // console.log(uBikeStops.value.length);

  pagesIndex.value += 10;

  if (pagesIndex.value > uBikeStops.value.length) {
    pagesIndex.value -= 10;
  }

  updatePage(pagesIndex.value +1);  // 翻頁時, 顯示第 1, 11, 21 ... 頁
}  

const subPagesIndex = () => {
  pagesIndex.value -= 10;
  if (pagesIndex.value < 0) {
    pagesIndex.value = 0;
  }

  updatePage(pagesIndex.value +1);  // 翻頁時, 顯示第 1, 11, 21 ... 頁
} 

</script>

<template>
<div class="app">

  {{ siteFilter }}

  <p>
    站點名稱搜尋: <input type="text" v-model="siteFilter">
  </p>

  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th @click="sortAvailbleCar()">目前可用車輛
          <i class="fa fa-sort-asc" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" aria-hidden="true"></i>
        </th>
        <th @click="sortAvailablePos()">總停車格
          <i class="fa fa-sort-asc" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" aria-hidden="true"></i>
        </th>
        <th>資料更新時間</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="s in siteResults" :key="s.sno">
        <td>{{ s.sno }}</td>
        <td>{{ s.sna }}</td>
        <td>{{ s.sarea }}</td>
        <td>{{ s.sbi }}</td>
        <td>{{ s.tot }}</td>
        <td>{{ timeFormat(s.mday) }}</td>
      </tr>
    </tbody>
  </table>

  <ul>
      <li class="pager"><a href="#" @click="subPagesIndex()"> &lt; </a></li>

      <li class="pager" v-for="n in 10" :key="n">
        <!-- n 從 1 開始計算 -->        
        <a href="#" @click="updatePage(n + pagesIndex)">{{ n + pagesIndex }}</a>
      </li>

      <li class="pager"><a href="#" @click="addPagesIndex()"> &gt; </a></li>
  </ul>

</div>
</template>

<style scoped>
.app {
  padding: 1rem;
}

.pager {
float: left;
display: block;
width: 30px;
height: 30px;
text-align: center;
line-height: 20px;
margin-right: 5px;
padding: 5px;
border: 1px solid #aaa;
margin-bottom: 15px;
}

.pager a {
text-decoration: none;
}

</style>