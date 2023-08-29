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


</script>

<template>
<div class="app">
  <div class="movie-list" v-for="item in movies">
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <!-- <img src="https://m.media-amazon.com/images/M/MV5BM2MyNjYxNmUtYTAwNi00MTYxLWJmNWYtYzZlODY3ZTk3OTFlXkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_FMjpg_UY1982_.jpg" class="movie-item-image" alt=""> -->
        <img :src="item.image" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{ item.name }}</h3>
          <div class="movie-item-genres-wrapper" >
            <span class="movie-item-genre-tag" v-for="gen in item.genres">{{ gen }}</span>
            <!-- <span class="movie-item-genre-tag">Drama</span> -->
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ item.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{item.rating}}/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button class="movie-item-star-icon-button" @click="$event.currentTarget.classList.contains('text-yellow-500') ? item.rating-- : item.rating++" :class="n <= item.rating ? 'text-yellow-500' : 'text-gray-500'" v-for="n in 5">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <!-- <button class="text-yellow-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-yellow-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-yellow-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-gray-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button> -->
          </div>
        </div>
      </div>
    </div>   
  </div>
</div>
</template>
