<script setup>
import { ref, computed, watch } from "vue";

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

const pageCnt = 20;
const uBikeStopsTotal = ref([]);
// TODO: 為何computed重複寫入
// let uBikeStops = computed({
//   get: () =>
//     uBikeStopsTotal.value ? uBikeStopsTotal.value.slice(0, page) : [],
//   set: (arr) => {
//     console.log(arr);
//     uBikeStops.value = arr;
//     return arr;
//   },
// });
let uBikeStops = ref(
  uBikeStopsTotal.value ? uBikeStopsTotal.value.slice(0, pageCnt) : []
);
let nowPage = ref(1);
const totalCnt = computed(() => {
  let ttlcnt = uBikeStopsTotal.value
    ? JSON.parse(JSON.stringify(uBikeStopsTotal.value)).length
    : 0;
  let finalPage = Math.ceil(ttlcnt / pageCnt);
  let n = 0;
  if (nowPage.value > 10) {
    n = Math.floor(nowPage.value / 10); // 取商
    // 10 20 30 ... 跳回
    if (nowPage.value % 10 == 0) {
      n -= 1;
    }
  }
  let last10Page = n == Math.floor(finalPage / 10) && finalPage % 10 != 0;
  let tmp = Array.from(
    { length: last10Page ? finalPage % 10 : 11 },
    (_, i) => i + n * 10 + 1
  );
  if (!last10Page) tmp.push(finalPage);
  return n == 0 ? [...tmp] : [1, n * 10, ...tmp];
});

let titleSortName = ref("");
let titleSortAsc = ref("");

fetch(
  "https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json"
)
  .then((res) => res.text())
  .then((data) => {
    uBikeStopsTotal.value = JSON.parse(data).sort((a, b) => a.sno - b.sno);
    paging(1);
  });

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, "$1/$2/$3 $4:$5:$6");
};

const searchStation = (event) => {
  let arr = JSON.parse(JSON.stringify(uBikeStopsTotal.value));
  arr = arr.filter((x) => x.sna.indexOf(event.target.value) >= 0);
  uBikeStopsTotal.value = arr;
  paging(1);
};

const sortByTitle = (title) => {
  titleSortAsc = titleSortName == title ? "desc" : "asc";
  titleSortName = title;
  let arr = JSON.parse(JSON.stringify(uBikeStopsTotal.value));
  arr = arr.sort((a, b) =>
    titleSortAsc == "asc" ? a[title] - b[title] : b[title] - a[title]
  );
  uBikeStopsTotal.value = arr;
};

const paging = (page) => {
  let arr = JSON.parse(JSON.stringify(uBikeStopsTotal.value));
  let begin = (+page - 1) * pageCnt;
  let end = begin + pageCnt > totalCnt ? totalCnt : begin + pageCnt;
  nowPage.value = page;
  arr = arr.slice(begin, end);
  uBikeStops.value = arr;
};
</script>

<template>
  <div class="app">
    <p>站點名稱搜尋: <input @input="searchStation($event)" type="text" /></p>
    <div class="body">
      <ul>
        <li class="pager"><a href="#"> &lt; </a></li>

        <li class="pager" v-for="n in totalCnt">
          <!-- n 從 1 開始計算 -->
          <a @click="paging(n)" href="#">{{ n }}</a>
        </li>

        <li class="pager"><a href="#"> &gt; </a></li>
      </ul>
    </div>

    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th @click="sortByTitle('sbi')">
            目前可用車輛
            <i
              v-if="
                titleSortName == '' ||
                (titleSortName == 'sbi' && titleSortAsc == 'asc')
              "
              class="fa fa-sort-asc"
              aria-hidden="true"
            ></i>
            <i
              v-if="
                titleSortName == '' ||
                (titleSortName == 'sbi' && titleSortAsc == 'desc')
              "
              class="fa fa-sort-desc"
              aria-hidden="true"
            ></i>
          </th>
          <th @click="sortByTitle('tot')">
            總停車格
            <i
              v-if="
                titleSortName == '' ||
                (titleSortName == 'tot' && titleSortAsc == 'asc')
              "
              class="fa fa-sort-asc"
              aria-hidden="true"
            ></i>
            <i
              v-if="
                titleSortName == '' ||
                (titleSortName == 'tot' && titleSortAsc == 'desc')
              "
              class="fa fa-sort-desc"
              aria-hidden="true"
            ></i>
          </th>
          <th>資料更新時間</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="s in uBikeStops" :key="s.sno">
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
body {
  padding: 1rem;
  font-size: 1.33rem;
}

.pager {
  float: left;
  display: block;
  text-align: center;
  line-height: 20px;
  margin-right: 5px;
  padding: 5px;
  border: 1px solid #aaa;
}

.pager a {
  text-decoration: none;
}
</style>
