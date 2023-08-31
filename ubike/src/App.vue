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

fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
  .then(res => res.text())
  .then(data => {
    uBikeStops.value = JSON.parse(data);
  });

const timeFormat = (val) => {
  // 時間格式
  const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
  return val.replace(pattern, '$1/$2/$3 $4:$5:$6');
};

const kw = ref( '' );

const orderBy目前可用車輛 = ref( {
  order : true,
  asc : true
} )

const orderBy總停車格 = ref( {
  order : false,
  asc : true
} )

const Paging = ref( {
  pageSize : 20,
  pageTotalPageNumber : 0,
  currentPage : 1
} )

const Search = computed( () => {
  set :
  {
      let stops = uBikeStops.value;

      if ( kw.value.trim().length !== 0 )
      {
        stops = uBikeStops.value.filter( x => x.sna.includes( kw.value ) );
      }

      if ( orderBy目前可用車輛.value.order )
      {
        if ( orderBy目前可用車輛.value.asc )
        {
          stops = stops.sort( ( a, b ) => Asc( a.sbi, b.sbi ) );
        }
        else
        {
          stops = stops.sort( ( a, b ) => Desc( a.sbi, b.sbi ) );
        }
      }
      else
      {
        if ( orderBy總停車格.value.asc )
        {
          stops = stops.sort( ( a, b ) => Asc( a.tot, b.tot ) );
        }
        else
        {
          stops = stops.sort( ( a, b ) => Desc( a.tot, b.tot ) );
        }
      }

      Paging.value.pageTotalPageNumber = stops.length % 20 === 0 ? ( stops.length / 20 ) : parseInt( stops.length / 20 ) + 1;

      const start = ( Paging.value.currentPage - 1 ) * 20;
      let end = ( Paging.value.currentPage ) * 20;
      end = end > stops.length ? stops.length : end;

      stops = stops.slice( start, end );

    return stops;
  }
})

const Order = ( val ) => {
  if ( val )
  {
    orderBy目前可用車輛.value.order = true;
    orderBy目前可用車輛.value.asc = !orderBy目前可用車輛.value.asc;
    orderBy總停車格.value.order = !orderBy目前可用車輛.value.order;
  }
  else
  {
    orderBy總停車格.value.order = true;
    orderBy總停車格.value.asc = !orderBy總停車格.value.asc;
    orderBy目前可用車輛.value.order = !orderBy總停車格.value.order;
  }
};

function Asc( a, b )
{
  if ( a < b ) {
    return -1;
  }
  if ( a > b )
  {
    return 1;
  }

  return 0;
}
function Desc( a, b )
{
  if ( a > b ) {
    return -1;
  }
  if ( a < b )
  {
    return 1;
  }

  return 0;
}
</script>

<template>
<div class="app">
  <p>
    站點名稱搜尋: <input type="text" v-model="kw">
  </p>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item" v-show="Paging.currentPage > 1">
        <a class="page-link" @click="Paging.currentPage = Paging.currentPage - 1" >pre</a>
      </li>
      <li class="page-item"
          :class="n === Paging.currentPage ? 'active' : ''"
          v-for="( n, index ) in Paging.pageTotalPageNumber" :key="index"
          v-show="n >= parseInt( n / 10 ) * 10 && n < ( Paging.currentPage ) + 10">
        <a class="page-link" @click="Paging.currentPage = n" >{{ n }}</a>
      </li>
      <li class="page-item" v-show="Paging.currentPage !== Paging.pageTotalPageNumber">
        <a class="page-link" @click="Paging.currentPage = Paging.currentPage + 1" >next</a>
      </li>
    </ul>
  </nav>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>#</th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th>目前可用車輛
          <i class="fa fa-sort-asc" v-if="orderBy目前可用車輛.asc" @click="Order( true )" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" v-else @click="Order( true )" aria-hidden="true"></i>
        </th>
        <th>總停車格
          <i class="fa fa-sort-asc" v-if="orderBy總停車格.asc" @click="Order( false )" aria-hidden="true"></i>
          <i class="fa fa-sort-desc" v-else @click="Order( false )" aria-hidden="true"></i>
        </th>
        <th>資料更新時間</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="s in Search" :key="s.sno">
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