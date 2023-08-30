<script setup>
import { ref, computed, watch, onMounted } from 'vue';

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
const uBikeStopsFilter = ref([]);
// const uBikeStopsFilterPaginated = ref([]);

const searchTxt = ref("");
const isSbiAsc = ref(false);
const isTotAsc = ref(false);

const perPageNum = ref(20);
const activePage = ref(0);
const activePageGroup = ref(0);

const isPrevBtn = ref(false);
const isNextBtn = ref(true);

// const paginatedArray = ref(0)

fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    const parsedData = JSON.parse(data);
    uBikeStops.value = parsedData;
    uBikeStopsFilter.value = parsedData;
  });

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};

//處理搜尋
const stopsSearch = () => {
  if (searchTxt.value)
    uBikeStopsFilter.value = uBikeStops.value.filter(stop => stop.sna.includes(searchTxt.value));
  else
    uBikeStopsFilter.value = uBikeStops.value;
  activePage.value = 0;
  activePageGroup.value = 0;
}

watch(uBikeStopsFilter, () => {
  if (uBikeStopsFilter.value.length <= perPageNum.value * 10) {
    isPrevBtn.value = false;
    isNextBtn.value = false;
  } else {
    isPrevBtn.value = false;
    isNextBtn.value = true;
  }
})

//處理排序
const toggleAsc = (val) => {
  if (val === 'sbi') {
    isSbiAsc.value = !isSbiAsc.value;
  } else if (val === 'tot') {
    isTotAsc.value = !isTotAsc.value;
  }
}

watch(isSbiAsc, (newSbiAsc) => {
  sortUbikeStops(newSbiAsc, 'sbi');
})

watch(isTotAsc, (newTotAsc) => {
  sortUbikeStops(newTotAsc, 'tot');
})

function sortUbikeStops(ase, property) {
  const order = ase ? 1 : -1;
  uBikeStopsFilter.value.sort((a, b) => (a[property] - b[property]) * order)

  activePage.value = 0;
  activePageGroup.value = 0;
}

//處理分頁功能
//1. 先把資料分組
//2. 把分好組的資料拿來作分頁
const uBikeStopsFilterPaginated = computed(() => {
  return paginatedUbikeStops(uBikeStopsFilter.value, perPageNum.value);
})

function paginatedUbikeStops(data, itemPerpage) {
  const paginatedData = [];

  for (let i = 0; i < data.length; i += itemPerpage) {
    paginatedData.push(data.slice(i, i + itemPerpage));
  }
  return paginatedData;
}

const showPaginatedStops = computed(() => {
  return uBikeStopsFilterPaginated.value[activePage.value];
})

const paginatedArray = computed(() => {
  const paginationTotalNum = uBikeStopsFilterPaginated.value.length;
  const tempArr = [];

  for (let i = 0; i < paginationTotalNum; i++) {
    tempArr.push(i);
  }

  const pageArrayObj = [];

  for (let i = 0; i < tempArr.length; i += 10) {
    pageArrayObj.push(tempArr.slice(i, i + 10))
  }
  return pageArrayObj;
})

const paginationShowGroup = computed(() => {
  return paginatedArray.value[activePageGroup.value]
})

function showPage(idx) {
  activePage.value = idx;
}

function changePageGroup(navigation) {
  const lastIndex = paginatedArray.value.length - 1;
  const maxPage = uBikeStopsFilterPaginated.value.length - 1;

  if (navigation === 'prev') {
    activePageGroup.value = Math.max(activePageGroup.value - 1, 0);
    activePage.value = Math.max(activePage.value - 10, 0);
  } else if (navigation === 'next') {
    if (activePageGroup.value < lastIndex) {
      activePage.value = Math.min(activePage.value + 10, maxPage);
      activePageGroup.value += 1;
    } else {
      activePage.value = paginatedArray.value[lastIndex][0];
    }
  }
  
  isPrevBtn.value = activePageGroup.value > 0;
  isNextBtn.value = activePageGroup.value < lastIndex;
}


// function changePageGroup(navigation) {
//   if (navigation === 'prev') {
//     activePageGroup.value ? activePageGroup.value -= 1 : activePageGroup.value = 0;
//     activePage.value -= 10;
//   } else if (navigation === 'next') {
//     if (activePageGroup.value < paginatedArray.value.length - 1) {
//       activePage.value = (activePage.value + 10 < uBikeStopsFilterPaginated.value.length) ? activePage.value + 10 : uBikeStopsFilterPaginated.value.length - 1;
//       activePageGroup.value += 1;
//     } else if (activePageGroup.value === paginatedArray.value.length - 1) {
//       activePage.value = paginatedArray.value[paginatedArray.value.length][0];
//       activePageGroup.value = paginatedArray.value.length - 1;
//     }
//   }
//   isPrevBtn.value = activePageGroup.value > 0;
//   isNextBtn.value = activePageGroup.value < paginatedArray.value.length - 1;

// }

</script>

<template>
  <div class="app">
    <p>
      站點名稱搜尋: <input type="text" v-model="searchTxt" @keyup.enter="stopsSearch()" placeholder="輸入完後請按下Enter鍵">
    </p>
    <p>資料總筆數{{ uBikeStopsFilter.length }}</p>
    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th class="sort-style" @click="toggleAsc('sbi')">
            目前可用車輛
            <i :class="[{ 'fa-sort-asc': isSbiAsc }, 'fa']" aria-hidden="true"></i>
            <i :class="[{ 'fa-sort-desc': !isSbiAsc }, 'fa']" aria-hidden="true"></i>
          </th>
          <th class="sort-style" @click="toggleAsc('tot')">總停車格
            <i :class="[{ 'fa-sort-asc': isTotAsc }, 'fa']" aria-hidden="true"></i>
            <i :class="[{ 'fa-sort-desc': !isTotAsc }, 'fa']" aria-hidden="true"></i>
          </th>
          <th>資料更新時間</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="s in showPaginatedStops" :key="s.sno">
          <td>{{ s.sno }}</td>
          <td>{{ s.sna }}</td>
          <td>{{ s.sarea }}</td>
          <td>{{ s.sbi }}</td>
          <td>{{ s.tot }}</td>
          <td>{{ timeFormat(s.mday) }}</td>
        </tr>
      </tbody>
    </table>

    <div v-if="uBikeStopsFilter.length <= 0" class="no-data">
      <p>sorry,there's no data.</p>
    </div>
    <nav v-if="uBikeStopsFilter.length > 0" class="page-navigation" aria-label="Page navigation example">
      <ul class="pagination justify-content-center">
        <li :class="[{ 'disabled': !isPrevBtn }, 'page-item']"><a class="page-link" href=""
            @click.prevent="changePageGroup('prev')">Previous 10 Pages</a></li>
        <template v-for="idx in paginationShowGroup ">
          <li :class="[{ 'active': idx === activePage }, 'page-item']"><a class="page-link" href=""
              @click.prevent="showPage(idx)">{{ idx + 1 }}</a></li>
        </template>
        <li :class="[{ 'disabled': !isNextBtn }, 'page-item']"><a class="page-link" href=""
            @click.prevent="changePageGroup('next')">Next 10 Pages</a></li>
      </ul>
    </nav>
  </div>
</template>

<style scoped>
.app {
  padding: 1rem;
}

th.sort-style:hover {
  cursor: pointer;
}

.no-data {
  text-align: center;
  color: gray;
}
</style>