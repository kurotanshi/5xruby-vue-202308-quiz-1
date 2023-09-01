<script setup>
import { ref, computed, watch } from 'vue';

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
const searchStr = ref('')
const filterStops = ref([])
const currentPage = ref(1)
const perPageStops = ref(20)
const pageRange = ref(10)
const totalPage = ref()
const paginationRange = ref(0)

const orderByAscending = (stopKey) => {
  filterStops.value.sort((a, b)=> a[stopKey] - b[stopKey])
}
const orderByDescending = (stopKey) => {
  filterStops.value.sort((a, b)=> b[stopKey] - a[stopKey])
}
const getStopsForPage = (pageNumber, apiData) => {
  const startIndex = (pageNumber - 1) * perPageStops.value
  const endIndex = startIndex + perPageStops.value
  const pageData = apiData.slice(startIndex, endIndex)

  return pageData
}
const setCurrentPage= (number) => {
  currentPage.value = number
}
const addPaginationRange = () => {
  if ((paginationRange.value + pageRange.value) <= totalPage.value ) {
    paginationRange.value += pageRange.value
  }
}
const minusPaginationRange = () => {
  if (Math.sign(paginationRange.value - pageRange.value) !== -1) {
    paginationRange.value -= pageRange.value
  }
}

watch(
  searchStr,
  (newVal) => {
    const filterStopsName = uBikeStops.value.filter((stop) => {
      return stop.sna.includes(newVal)
    })

    filterStops.value = newVal && filterStopsName.length > 0 ? getStopsForPage(1, filterStopsName) : getStopsForPage(1, uBikeStops.value)
  }
)
watch(
  currentPage,
  (newVal) => {
    if(newVal) {
      filterStops.value = getStopsForPage(newVal, uBikeStops.value)
    }
  }
)

fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
    totalPage.value = Math.ceil(uBikeStops.value.length / perPageStops.value)
    filterStops.value = getStopsForPage(currentPage.value, uBikeStops.value)
  });

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};
</script>

<template>
<div class="app">
  <p>
    站點名稱搜尋: <input type="text" v-model="searchStr">
  </p>

  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th>目前可用車輛
          <i class="fa fa-sort-asc" aria-hidden="true" @click="orderByAscending('sbi')"></i>
          <i class="fa fa-sort-desc" aria-hidden="true" @click="orderByDescending('sbi')"></i>
        </th>
        <th>總停車格
          <i class="fa fa-sort-asc" aria-hidden="true" @click="orderByAscending('tot')"></i>
          <i class="fa fa-sort-desc" aria-hidden="true"  @click="orderByDescending('tot')"></i>
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
    <ul>
      <li class="pager"><a href="##" @click="minusPaginationRange"> &lt; </a></li>
      <template v-for="n in pageRange">
        <li class="pager" v-if="n + paginationRange < totalPage">
          <a :class="{'currentPage': (n + paginationRange) === currentPage}" href="#" @click="setCurrentPage((n + paginationRange))">
            {{ n + paginationRange }}
          </a>
        </li>
      </template>
      <li class="pager"><a href="##" @click="addPaginationRange"> &gt; </a></li>
    </ul>
</div>
</template>

<style lang="scss" scoped>
.app {
  padding: 1rem;
}
th i {
  cursor: pointer;
}
ul {
  padding: unset;
  .pager {
    font-size: 1.33rem;
    float: left;
    display: block;
    width: 30px;
    height: 30px;
    text-align: center;
    line-height: 30px;
    margin-right: 5px;
    border: 1px solid #aaa;

    a {
      text-decoration: none;
      &.currentPage {
        font-weight: bold;
      }
    }
  }
}
</style>