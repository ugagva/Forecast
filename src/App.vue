<script setup>

import { API_KEY, BASE_URL } from '@/constants/index.js'
import { ref, onMounted, computed } from 'vue'

import WeatherSummary from './components/WeatherSummary.vue'
import HumidityMenu from '@/components/HumidityMenu.vue'
import CoordsMenu from '@/components/CoordsMenu.vue'
import HighlightMenu from '@/components/HighlightMenu.vue'
import { capitalizeFirstLetter } from './utils/index.js'


const city = ref('Paris')
const weatherInfo = ref(null)
const isError = computed(() => weatherInfo.value?.cod !== 200)

function getWeather() {
  fetch(`${BASE_URL}?q=${city.value}&units=metric&appid=${API_KEY}`)
    .then((res) => res.json())
    .then((data) => weatherInfo.value = data)
}

onMounted(getWeather)

</script>

<template>
  <div class="page">
    <main class="main">
      <div class="container">
        <div class="laptop">
          <div class="sections">
            <section :class="['section', 'section-left',{'section-error':isError }]">
              <div class="info">
                <div class="city-inner">
                  <input v-model="city"
                         type="text"
                         class="search"
                         @keyup.enter="getWeather">
                </div>
                <WeatherSummary v-if="!isError" :weatherInfo="weatherInfo" />
                <div v-else class="error">
                  <div class="error-title">
                    Ooops!
                  </div>
                  <div v-if="weatherInfo?.message"  class="error-message">
                    {{ capitalizeFirstLetter(weatherInfo?.message) }}
                  </div>


                </div>

              </div>
            </section>
            <section v-if="!isError" class="section section-right">
              <HighlightMenu :weatherInfo="weatherInfo" />
            </section>
          </div>
          <div v-if="!isError" class="sections">
            <CoordsMenu :coord="weatherInfo.coord" />
            <HumidityMenu :humidity="weatherInfo.main.humidity" />
          </div>
        </div>
      </div>
    </main>
  </div>

</template>

<style lang="sass" scoped>
@import "assets/styles/main"
.page
  position: relative
  display: flex
  justify-content: center
  align-items: center
  min-height: 100vh
  padding: 20px 0
  background-color: #59585d

.laptop
  width: 100%
  padding: 20px
  background-color: #0e100f
  border-radius: 25px

.sections
  display: flex
  width: 100%

  @media (max-width: 767px)
    flex-direction: column

.section-left

  width: 30%
  padding-right: 10px

  @media (max-width: 767px)
    width: 100%
    padding-right: 0

  &.section-error
  min-width: 280px
  width: auto
  padding-right: 0


.section-right
  width: 70%
  padding-left: 10px

  @media (max-width: 767px)
    width: 100%
    margin-top: 16px
    padding-left: 0

.city-inner
  position: relative
  display: inline-block
  width: 100%

  &::after
    content: ''
    position: absolute
    top: 0
    right: 10px
    width: 25px
    height: 25px
    background: url('./assets/img/search.svg') no-repeat 50% 50%
    background-size: contain
    transform: translateY(50%)
    cursor: pointer

.info
  height: 100%
  padding: 16px
  background: url('./assets/img/gradient-1.jpg') no-repeat 50% 50%
  background-size: cover
  border-radius: 25px

.search
  width: 100%
  padding: 16px
  font-family: 'Inter', Arial, sans-serif
  color: $white
  background-color: rgba(0, 0, 0, 0.75)
  border-radius: 16px
  border: none
  outline: none
  cursor: pointer

.section-bottom
  width: 50%
  margin-top: 16px

  @media (max-width: 767px)
    width: 100%

.error
  padding-top: 20px

  &-title
    font-size: 18px
    font-weight: 700
  &-message
    color: #ef060e
    font-size: 16px
    padding-top: 10px

  </style>

