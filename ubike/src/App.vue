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

const originalUBikeStops = ref([]);
const uBikeStops = ref([]);

// API
const fetchData = async () => {
  try {
    const response = await fetch(
      "https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json"
    );
    const data = await response.json();
    originalUBikeStops.value = data;
    uBikeStops.value = data;
  } catch (error) {
    console.error(error);
  }
};
fetchData();

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, "$1/$2/$3 $4:$5:$6");
};

// 搜尋輸入值
const searchInput = ref("");

// 站點搜尋
const searchData = computed(() => {
  return uBikeStops.value.filter((item) => item.sna.match(searchInput.value));
});

const searchPerform = () => {
  searchData.value.length > 0
    ? (uBikeStops.value = searchData.value)
    : (uBikeStops.value = originalUBikeStops.value);
};

// 搜尋重置
const searchReset = () => {
  searchInput.value = "";
  uBikeStops.value = originalUBikeStops.value;
};

// 箭頭 Class
const highArrow = ref("fa fa-sort-asc");
const lowArrow = ref("fa fa-sort-desc");

// 排序資料 - 柯里化接收 data 參數
const sortData = (data) => (event) => {
  const arrowClassName = event.target.className;
  const sortFunction =
    arrowClassName === highArrow.value
      ? (a, b) => b[data] - a[data]
      : (a, b) => a[data] - b[data];
  uBikeStops.value = uBikeStops.value.sort(sortFunction);
};
</script>

<template>
  <div class="app container">
    <header class="row align-items-center">
      <div class="col-md-3">
        <h1>
          <a href="#">
            <img src="https://www.youbike.com.tw/region/assets/images/logo_180x180.png" alt="U Bike Logo">
          </a>
        </h1>
      </div>
      <div class="col-md-6">
        <input
          class="form-control"
          type="text"
          placeholder="請輸入 ..."
          v-model="searchInput"
          @change="searchPerform"
        />
      </div>
      <div class="col-md-3">
        <div class="my-2 my-md-0">
          <button class="btn btn-primary mr-2" @click="searchPerform">
            搜尋
          </button>
          <button class="btn btn-primary mr-2" @click="searchReset">
            重置
          </button>
        </div>
      </div>
    </header>
    <!-- 表格 -->
    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th>
            目前可用車輛
            <i
              :class="highArrow"
              @click="sortData('sbi')($event)"
              aria-hidden="true"
            ></i>
            <i
              :class="lowArrow"
              @click="sortData('sbi')($event)"
              aria-hidden="true"
            ></i>
          </th>
          <th>
            總停車格
            <i
              :class="highArrow"
              @click="sortData('tot')($event)"
              aria-hidden="true"
            ></i>
            <i
              :class="lowArrow"
              @click="sortData('tot')($event)"
              aria-hidden="true"
            ></i>
          </th>
          <th>資料更新時間</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(s, idx) in uBikeStops" :key="s.sno">
          <td>{{ idx + 1 }}</td>
          <td>{{ s.sna }}</td>
          <td>{{ s.sarea }}</td>
          <td>{{ s.sbi }}</td>
          <td>{{ s.tot }}</td>
          <td>{{ timeFormat(s.mday) }}</td>
        </tr>
      </tbody>
    </table>
    <!-- 分頁： 尚未完成  -->
    <nav aria-label="Page navigation">
      <ul class="pagination d-flex justify-content-end">
        <li class="page-item">
          <a class="page-link" href="#" aria-label="Previous">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li class="page-item active"><a class="page-link" href="#">1</a></li>
        <li class="page-item"><a class="page-link" href="#">2</a></li>
        <li class="page-item"><a class="page-link" href="#">3</a></li>
        <li class="page-item"><a class="page-link" href="#">4</a></li>
        <li class="page-item"><a class="page-link" href="#">5</a></li>
        <li class="page-item">
          <a class="page-link" href="#" aria-label="Next">
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
  </div>
</template>

<style scoped>
.app {
  padding: 1rem;
}
.fa {
  font-size: 1.25rem;
  padding: 0.125rem;
  transition: 0.2s;
  cursor: pointer;
}
.fa:hover {
  color: lightskyblue;
  transform: scale(1.5);
}
</style>