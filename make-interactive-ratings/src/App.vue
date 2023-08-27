<script setup>
/**
完成以下功能：
1. 將 movies 的內容用 v-for 改寫成動態渲染
2. 完成電影星星評分的事件綁定，同樣用 v-for 進行改寫
*/

import { ref } from 'vue';
import { StarIcon } from '@heroicons/vue/24/solid';
import { items } from './movies.json';

const movies = ref(items);

const setRating = (i, n) => (movies.value[i].rating = n);
</script>

<template>
  <div class="app">
    <div class="movie-list">
      <div class="movie-item" v-for="(m, index) in movies" :key="m.id">
        <div class="movie-item-image-wrapper">
          <img :src="m.image" class="movie-item-image" alt="" />
        </div>
        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ m.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span class="movie-item-genre-tag" v-for="g in m.genres">
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
              <template v-for="(n, i) in 5">
                <button
                  class="movie-item-star-icon-button"
                  :class="i < m.rating ? 'text-yellow-500' : 'text-gray-500'"
                  @click="setRating(index, n)"
                >
                  <StarIcon class="movie-item-star-icon" />
                </button>
              </template>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
