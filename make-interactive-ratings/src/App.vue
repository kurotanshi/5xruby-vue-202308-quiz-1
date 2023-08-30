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
const starChange = (id, num) =>{
  console.log(movies.value[id].rating);
  console.log(id);
  console.log(num);
  movies.value[id].rating = num;
  console.log(movies.value[id].rating);
}
</script>

<template>
<div class="app">
  <div class="movie-list" v-for="(item, idx) in movies" :key="item.id">
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <img :src="item.image" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{item.name}}</h3>
          <div class="movie-item-genres-wrapper" v-for="g in item.enres">
            <span class="movie-item-genre-tag">{{ g }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ item.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: {{item.rating}} </span>
          
          <div class="movie-item-star-icon-wrapper" v-for=" hoshi of 5">
            <button class="text-yellow-500 movie-item-star-icon-button" @click="starChange(idx, hoshi)" v-if="hoshi <= item.rating">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-gray-500 movie-item-star-icon-button" @click="starChange(idx, hoshi)" v-if="hoshi > item.rating">
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
