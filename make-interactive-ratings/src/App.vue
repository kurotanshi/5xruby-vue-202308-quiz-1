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
  <div class="movie-list">
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <img src="https://m.media-amazon.com/images/M/MV5BM2MyNjYxNmUtYTAwNi00MTYxLWJmNWYtYzZlODY3ZTk3OTFlXkEyXkFqcGdeQXVyNzkwMjQ5NzM@._V1_FMjpg_UY1982_.jpg" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">The Godfather</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag">Crime</span>
            <span class="movie-item-genre-tag">Drama</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">The aging patriarch of an organized crime dynasty transfers control of his clandestine empire to his reluctant son.</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: (4/5) </span>
          
          <div class="movie-item-star-icon-wrapper">
            <button class="text-yellow-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-yellow-500 movie-item-star-icon-button">
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
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <img src="https://m.media-amazon.com/images/M/MV5BNDE3ODcxYzMtY2YzZC00NmNlLWJiNDMtZDViZWM2MzIxZDYwXkEyXkFqcGdeQXVyNjAwNDUxODI@._V1_FMjpg_UX1200_.jpg" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">The Shawshank Redemption</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag">Drama</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">Two imprisoned men bond over a number of years, finding solace and eventual redemption through acts of common decency.</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: (4/5) </span>
          <div class="movie-item-star-icon-wrapper">
            <button class="text-yellow-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
            <button class="text-yellow-500 movie-item-star-icon-button">
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
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="movie-item">
      <div class="movie-item-image-wrapper">
        <img src="https://m.media-amazon.com/images/M/MV5BMTMxNTMwODM0NF5BMl5BanBnXkFtZTcwODAyMTk2Mw@@._V1_FMjpg_UY2048_.jpg" class="movie-item-image" alt="">
      </div>
      <div class="movie-item-content-wrapper">
        <div class="movie-item-title-wrapper">
          <h3 class="movie-item-title">The Dark Knight</h3>
          <div class="movie-item-genres-wrapper">
            <span class="movie-item-genre-tag">Action</span>
            <span class="movie-item-genre-tag">Crime</span>
            <span class="movie-item-genre-tag">Drama</span>
          </div>
        </div>
        <div class="movie-item-description-wrapper">
          <p class="movie-item-description">When the menace known as the Joker wreaks havoc and chaos on the people of Gotham, Batman must accept one of the greatest psychological and physical tests of his ability to fight injustice.</p>
        </div>
        <div class="movie-item-rating-wrapper">
          <span class="movie-item-rating-text"> Rating: (3/5) </span>
          <div class="movie-item-star-icon-wrapper">
            <button class="text-yellow-500 movie-item-star-icon-button">
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
            </button>
            <button class="text-gray-500 movie-item-star-icon-button">
              <StarIcon class="movie-item-star-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
