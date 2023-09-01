<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref} from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);
const setRating = (id, score) => {
  const movie = movies.value.find((item) => item.id === id); // 尋找並比對 id 相符資料
  if (movie) {
    movie.rating = score; // movie 資料中的 rating 被賦予點擊星星分數的值 
  }
};
</script>

<template>
  <div class="app">
    <div class="movie-list">
      <div class="movie-item" v-for="item in movies" :key="item.id">
        <div class="movie-item-image-wrapper">
          <img :src="item.image" class="movie-item-image" alt="img" />
        </div>
        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ item.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span
                class="movie-item-genre-tag"
                v-for="genre in item.genres"
                :key="genre"
              >
                {{ genre }}
              </span>
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ item.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper">
            <span class="movie-item-rating-text">
              Rating: ({{ item.rating }}/5)
            </span>

            <div class="movie-item-star-icon-wrapper">
              <button
                v-for="(score, index) in 5"
                :key="index"
                class="movie-item-star-icon-button"
                :class="
                  index < item.rating ? 'text-yellow-500' : 'text-gray-500'
                "
                @click="setRating(item.id, score)"
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

