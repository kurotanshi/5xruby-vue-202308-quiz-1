<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);

// ??? 變數用 movie-item-src 編譯出錯, 改 movie_item_src OK 
const movie_item_src = [
  'https://m.media-amazon.com/images/M/MV5BM2MyNjYxNmUtYTAwNi00MTYxLWJmNWYtYzZlODY3ZTk3OTFlXkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_FMjpg_UY1982_.jpg',
  'https://m.media-amazon.com/images/M/MV5BNDE3ODcxYzMtY2YzZC00NmNlLWJiNDMtZDViZWM2MzIxZDYwXkEyXkFqcGdeQXVyNjAwNDUxODI@._V1_FMjpg_UX1200_.jpg',
  'https://m.media-amazon.com/images/M/MV5BMTMxNTMwODM0NF5BMl5BanBnXkFtZTcwODAyMTk2Mw@@._V1_FMjpg_UY2048_.jpg'
  ];

const movie_item_title = ['The Godfather', 'The Shawshank Redemption', 'The Dark Knight'];

const movie_item_tag = [
  ['Crime', 'Drama'],
  ['Drama'],
  ['Action', 'Crime', 'Drama']
];

const movie_item_description = [
  'The aging patriarch of an organized crime dynasty transfers control of his clandestine empire to his reluctant son.',
  'Two imprisoned men bond over a number of years, finding solace and eventual redemption through acts of common decency.',
  'When the menace known as the Joker wreaks havoc and chaos on the people of Gotham, Batman must accept one of the greatest psychological and physical tests of his ability to fight injustice.',
  ''
];  

const ratings = ref(1);

const starStyle_yellow = 'text-yellow-500 movie-item-star-icon-button';
const starStyle_gray   = 'text-gray-500 movie-item-star-icon-button';

const button_class = ref([starStyle_yellow, starStyle_gray, starStyle_gray, starStyle_gray, starStyle_gray]);

// 測試 button 的 v-for="n in 5", n 從 1 到 5
const testMsg = (msg = '') => alert(msg);

const setRattings = (clickRate =  1) => {
  ratings.value = clickRate;

  for (let i = 1; i <= 5; i++) {

    if (i <= clickRate) {
      button_class.value[i-1] = starStyle_yellow;
    } else {
      button_class.value[i-1] = starStyle_gray;
    }

  }

}

</script>

<template>
<div class="app">
  <div class="movie-list">
    <div class="movie-item" v-for="(src, idx) in movie_item_src" :key="src">
      <div class="movie-item-image-wrapper">
        <img v-bind:src=movie_item_src[idx] class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title"> {{ movie_item_title[idx] }} </h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag" v-for="(item) in movie_item_tag[idx]" :key="item"> {{ item }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description"> {{ movie_item_description[idx] }} </p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{ ratings }}/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button @click="setRattings(n)" v-bind:class=button_class[n-1] v-for="n in 5" :key="n">
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
