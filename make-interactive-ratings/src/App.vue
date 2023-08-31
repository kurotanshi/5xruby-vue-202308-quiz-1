<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import {computed, ref} from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref( items );

const Rate = ( rateCounter, movieIndex ) => {
  movies.value[ movieIndex ].rating = rateCounter;
}
</script>

<template>
<div class="app">
  <div class="movie-list">
    <div class="movie-item" v-for="( item, movieIndex ) in movies" :key="item.id">
      <div class="movie-item-image-wrapper">
        <img :src="item.image" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">{{ item.name }}</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag" v-for="tag in item.genres ">{{ tag }}</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">{{ item.description }}</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: ({{ item.rating }}/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button v-for="counter in 5"
                    :class="counter <= item.rating ? 'text-yellow-500 movie-item-star-icon-button'
                                                    : 'text-gray-500 movie-item-star-icon-button' "
                    @click="Rate( counter, movieIndex )" >
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
