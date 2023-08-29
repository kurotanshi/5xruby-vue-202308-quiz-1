<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { computed, ref } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);
const checkStar = (i,rating) => {
  if (i <= rating){
    return 'text-yellow-500';
  }else{
    return 'text-gray-500';
  }
};
const changeRating = (i,movie) => {
  movie.rating = i;
};


</script>

<template>
<div class="app">
  <div class="movie-list" v-for="movie in movies" :key="movie.id">
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <img :src="movie.image" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{ movie.name }}</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag" v-for="(item,index) in movie.genres" :key="index+1">{{ item }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ movie.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text">{{ `Rating:(${movie.rating}/5)` }} </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button class="movie-item-star-icon-button hover:text-yellow-500" :class="checkStar(i,movie.rating)"
             v-for="i in 5" :key="i+1"
             @click="changeRating(i,movie)"
             >
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
