<script setup>
import { ref, computed, watch,} from 'vue'

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

// 渲染用陣列
const uBikeStops = ref([])

fetch(
  'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
)
  .then((res) => res.text())
  .then((data) => {
    uBikeStops.value = JSON.parse(data)
    uBikeData.value = JSON.parse(data)
    // 非同步 頁籤先在這處理
    pages.value = Math.ceil(uBikeData.value.length / 20)
    uBikeStops.value.length = 20
  })

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6')
}

// 搜尋站名
const uBikeData = ref()   //操作用陣列
const snaSearch = ref('')

watch(snaSearch, (newSna) => {
  console.log(newSna)
  const searchStops = uBikeData.value.filter(
    (v) => v.sna.indexOf(newSna) !== -1
  )
  uBikeStops.value = searchStops
  pages.value = Math.ceil(searchStops.length / 20)
  uBikeStops.value.length >=20 ?  uBikeStops.value.length=20 : ''
})

//排序

const sort = (sortWay, sortFor) => {
  if (sortWay === 'asc') {
    uBikeStops.value.sort((a, b) => {
      return a[sortFor] - b[sortFor]
    })
  } else {
    uBikeStops.value.sort((a, b) => {
      return b[sortFor] - a[sortFor]
    })
  }
}

// 頁籤
  const pages = ref()
  const pageLimit =ref(10)
  const pageFor = (page) =>{
    uBikeStops.value = uBikeData.value.slice((page-1)*20,((page-1)*20)+19) 
  }
  const pageMove = (way) =>{
    if (way === 'left' && pageLimit.value === 10)return
    if (way === 'right' && pageLimit.value >= pages.value )return
    way === 'left' ?pageLimit.value = pageLimit.value -10 :pageLimit.value= pageLimit.value + 10
  }

</script>

<template>
  <div class="app">
    <p>站點名稱搜尋: <input type="text" v-model="snaSearch" /></p>

    <table class="table table-striped">
      <thead>
        <tr>
          <th>#</th>
          <th>場站名稱</th>
          <th>場站區域</th>
          <th>
            目前可用車輛
            <i
              class="fa fa-sort-asc"
              aria-hidden="true"
              @click="sort('asc', 'sbi')"
            ></i>
            <i
              class="fa fa-sort-desc"
              aria-hidden="true"
              @click="sort('desc', 'sbi')"
            ></i>
          </th>
          <th>
            總停車格
            <i
              class="fa fa-sort-asc"
              aria-hidden="true"
              @click="sort('asc', 'tot')"
            ></i>
            <i
              class="fa fa-sort-desc"
              aria-hidden="true"
              @click="sort('desc', 'tot')"
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
  <ul>
    <li class="pager"><a href="#" @click="pageMove('left')"> &lt; </a></li>
    <li class="pager" v-for="n of pages" v-show="pageLimit-10 < n && n <=pageLimit">
      <!-- n 從 1 開始計算 -->
      <a href="#" @click="pageFor(n)">{{ n }}</a>
    </li>
    <li class="pager"><a href="#" @click="pageMove('right')" > &gt; </a></li>
  </ul>
</template>

<style scoped>
.app {
  padding: 1rem;
}
.pager {
  float: left;
  display: block;
  width: 40px;
  height: 30px;
  text-align: center;
  line-height: 20px;
  margin-right: 5px;
  padding: 5px;
  border: 1px solid #aaa;
  margin-bottom: 30px;
}

.pager a {
  text-decoration: none;
}
</style>
