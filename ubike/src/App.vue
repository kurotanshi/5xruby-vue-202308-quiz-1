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
fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
  });

  // 搜尋排序
const filterStops = computed({
  get: () => inputName.value ? uBikeStops.value.filter(item => item.sna.match(inputName.value)) : uBikeStops.value,
  set: (val) => {
    if(val.choose == 'sbi'){
      if (val.sort) {
        uBikeStops.value.sort( (x,y) => x.sbi > y.sbi ? 1: -1 )
      }else{
        uBikeStops.value.sort( (x,y) => x.sbi < y.sbi ? 1: -1 )
      }
    }
    if(val.choose == 'tot'){
      if(val.sort) {
        uBikeStops.value.sort( (x,y) => x.tot > y.tot ? 1: -1 )
      }else{
        uBikeStops.value.sort( (x,y) => x.tot < y.tot ? 1: -1 )
      }
    }
  }
})

// click 觸發排序 
const sbiSort = () => {
  sbiAsc.value = !sbiAsc.value;
  filterStops.value = { sort:sbiAsc.value, choose:'sbi' }
  
}
const totSort = () => {
  totAsc.value = !totAsc.value;
  filterStops.value = { sort:totAsc.value, choose:'tot' }
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

  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th>目前可用車輛
          <a href=""  @click.prevent="sbiSort"><i class="fa fa-sort-asc" aria-hidden="true"></i><i class="fa fa-sort-desc" aria-hidden="true"></i></a>
        </th>
        <th>總停車格
          <a href=""  @click.prevent="totSort"><i class="fa fa-sort-asc" aria-hidden="true"></i><i class="fa fa-sort-desc" aria-hidden="true"></i></a>
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
</div>
</template>

<style scoped>
.app {
  padding: 1rem;
}
</style>