<script setup>
/** 
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/
import { ref, computed, watch } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";

const movies = ref(items);

const ratingText = (value) => {
  return `Rating: (${value}/5)`
}

const changeRatingStar = (color, num, mid) => {
  const movie = movies.value.find(movie => movie.id === mid);

  if (color === 'y') {
    movie.rating = num;
  } else if (color === 'g') {
    movie.rating += num;
  }

}

const movieRating = computed(() =>
  movies.value.reduce((acc, item) => {
    acc[item.id] = {
      yellowStar: item.rating,
      grayStar: 5 - item.rating
    }
    return acc;
  }, {})
);

</script>

<template>
  <div class="app">
    <div class="movie-list">
      <div class="movie-item" v-for="m in movies" :key="m.id">
        <div class="movie-item-image-wrapper">
          <img :src="m.image" class="movie-item-image" alt="">
        </div>
        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ m.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span class="movie-item-genre-tag" v-for=" g in m.genres">{{ g }}</span>
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ m.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper" v-if="movieRating[m.id]">
            <span class="movie-item-rating-text" v-text="ratingText(movieRating[m.id].yellowStar)"></span>
            <div class="movie-item-star-icon-wrapper">
              <template v-for="(starCount, color) in movieRating[m.id]">
                <button v-for="(count, idx) in starCount" :key="`${m.id}_${color}_${idx}`" :class="{
                  'text-yellow-500': color === 'yellowStar',
                  'text-gray-500': color === 'grayStar',
                }" @click="changeRatingStar(color.charAt(0), idx + 1, m.id)">
                  <StarIcon class="movie-item-star-icon" />
                </button>
              </template>
              <!-- <button class="text-yellow-500 movie-item-star-icon-button"
                v-for=" (y, idx) in movieRating[m.id].yellowStar"
                @click="changeRatingStar('y', idx + 1, m.id)">
                <StarIcon class="movie-item-star-icon" />
              </button>
              <button class="text-gray-500 movie-item-star-icon-button" v-for=" (g, idx) in movieRating[m.id].grayStar"
                @click="changeRatingStar('g', idx + 1, m.id)">
                <StarIcon class="movie-item-star-icon" />
              </button> -->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
