<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(JSON.parse(JSON.stringify(items)));
const setRating = (id, rating) => {
  let ms = JSON.parse(JSON.stringify(movies.value));
  let mk = ms.filter((x) => x.id != id);
  let m = ms.filter((x) => x.id == id)[0];
  m = {
    ...m,
    rating: rating,
  };
  movies.value = [...mk, m].sort((a, b) => a.id - b.id);
};
</script>

<template>
  <div class="app">
    <div class="movie-list">
      <div class="movie-item" v-for="m in movies">
        <div class="movie-item-image-wrapper">
          <img :src="m.image" class="movie-item-image" alt="" />
        </div>
        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ m.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span v-for="g in m.genres" class="movie-item-genre-tag">
                {{ g }}
              </span>
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ m.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper">
            <span class="movie-item-rating-text">
              Rating: ({{ m.rating }}/5)
            </span>

            <div class="movie-item-star-icon-wrapper">
              <button
                v-for="n in m.rating"
                @click="setRating(m.id, n)"
                class="text-yellow-500 movie-item-star-icon-button"
              >
                <StarIcon class="movie-item-star-icon" />
              </button>
              <button
                v-for="n in 5 - m.rating"
                @click="setRating(m.id, n + m.rating)"
                class="text-gray-500 movie-item-star-icon-button"
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
