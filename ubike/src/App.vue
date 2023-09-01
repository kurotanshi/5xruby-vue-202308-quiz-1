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

const ITEMS_PER_PAGE = 20; // 一頁二十筆資料
const activePage = ref(1); // 當前分頁

const ITEMS_PER_NAV = 10; // 一次顯示十個數字在導覽列

const uBikeStops = ref([]);

fetch(
  'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
)
  .then((res) => res.text())
  .then((data) => {
    uBikeStops.value = JSON.parse(data);
  });

// 搜尋字串
const searchString = ref('');

const filteredData = computed(() => {
  // 搜尋
  const tempArr = uBikeStops.value.filter((s) =>
    s.sna.includes(searchString.value)
  );

  // 排序
  if (columnOrderBy.value === '' || columnOrderBy.value === 'asc') {
    tempArr.sort((a, b) => a[column.value] - b[column.value]);
  } else {
    tempArr.sort((a, b) => b[column.value] - a[column.value]);
  }

  return tempArr;
});

// 總分頁數
const totalPages = computed(() =>
  Math.ceil(filteredData.value.length / ITEMS_PER_PAGE)
);

// 建立一個跟"總分頁數"相同長度的連續數字陣列
const totalPagesArray = computed(() =>
  [...Array(totalPages.value).keys()].map((i) => i + 1)
);

const totalNav = computed(() => Math.ceil(totalPages.value / ITEMS_PER_NAV));

const activeNav = computed(() => Math.ceil(activePage.value / ITEMS_PER_NAV));

const itemsInNav = computed(() =>
  totalPagesArray.value.slice(
    (activeNav.value - 1) * ITEMS_PER_NAV,
    activeNav.value * ITEMS_PER_NAV
  )
);

// 分頁資料
const itemsInPage = computed(() =>
  filteredData.value.slice(
    (activePage.value - 1) * ITEMS_PER_PAGE,
    activePage.value * ITEMS_PER_PAGE
  )
);

const column = ref(''); // 要排序的欄位
const columnOrderBy = ref(''); // 排序方法

const sortColumn = (col) => {
  if (column.value !== col) {
    columnOrderBy.value = '';
  }

  column.value = col;

  activePage.value = 1;

  if (columnOrderBy.value === 'asc') {
    columnOrderBy.value = 'desc';
  } else {
    columnOrderBy.value = 'asc';
  }
};

// 後十頁
const nextNav = () => {
  if (activeNav.value < totalNav.value) {
    activePage.value = itemsInNav.value[0] + ITEMS_PER_NAV;
  }
};

// 前十頁
const previousNav = () => {
  if (activeNav.value > 1) {
    activePage.value = itemsInNav.value[0] - 1;
  }
};

const setActivePage = (n) => (activePage.value = n);

// 搜尋字串有變動的話 reset
watch(searchString, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    column.value = '';
    columnOrderBy.value = '';
    activePage.value = 1;
  }
});
</script>

<template>
  <div class="app">
    <p>站點名稱搜尋: <input type="text" v-model="searchString" /></p>

    <nav aria-label="Page navigation">
      <ul class="pagination">
        <li
          class="page-item"
          :class="{ disabled: activeNav <= 1 }"
          @click="previousNav"
        >
          <a class="page-link" href="javascript:;">前十頁</a>
        </li>
        <li
          class="page-item"
          :class="{ active: n === activePage }"
          v-for="n in itemsInNav"
          @click="setActivePage(n)"
        >
          <a class="page-link" href="javascript:;">{{ n }}</a>
        </li>
        <li
          class="page-item"
          :class="{ disabled: activeNav >= totalNav }"
          @click="nextNav"
        >
          <a class="page-link" href="javascript:;">後十頁</a>
        </li>
      </ul>
    </nav>

    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th @click="sortColumn('sbi')" class="sort">
            目前可用車輛
            <i
              class="fa fa-sort-asc"
              :class="{ active: column === 'sbi' && columnOrderBy === 'asc' }"
              aria-hidden="true"
            ></i>
            <i
              class="fa fa-sort-desc"
              :class="{ active: column === 'sbi' && columnOrderBy === 'desc' }"
              aria-hidden="true"
            ></i>
          </th>
          <th @click="sortColumn('tot')" class="sort">
            總停車格
            <i
              class="fa fa-sort-asc"
              :class="{ active: column === 'tot' && columnOrderBy === 'asc' }"
              aria-hidden="true"
            ></i>
            <i
              class="fa fa-sort-desc"
              :class="{ active: column === 'tot' && columnOrderBy === 'desc' }"
              aria-hidden="true"
            ></i>
          </th>
          <th>資料更新時間</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="s in itemsInPage" :key="s.sno">
          <td>{{ s.sno }}</td>
          <td>{{ s.sna }}</td>
          <td>{{ s.sarea }}</td>
          <td>{{ s.sbi }}</td>
          <td>{{ s.tot }}</td>
          <td>{{ s.mday }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.app {
  padding: 1rem;
}

.sort {
  cursor: pointer;
}

.active {
  color: var(--primary);
}
</style>
