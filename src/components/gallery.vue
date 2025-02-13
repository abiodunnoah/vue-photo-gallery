<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";
import Card from "./card.vue";

const accessKey = import.meta.env.VITE_UNSPLASH_ACCESS_KEY;
const photos = ref(null);

const getPhotos = async () => {
  try {
    const response = await axios.get("https://api.unsplash.com/photos", {
      headers: {
        Authorization: `Client-ID ${accessKey}`,
      },
    });
    console.log((photos.value = response.data));
  } catch (error) {
    console.log("Error fetching Unsplash photos", error);
  }
};

onMounted(getPhotos);
console.log(photos.value);
</script>

<template>
  <nav>
    <div class="nav">
      <h1 class="header">Photos Gallery</h1>
      <input type="search" placeholder="Search..." class="search" />
    </div>
  </nav>
  <div class="gallery">
    <div v-for="photo in photos" :key="photo.id" class="photo">
      <img
        :src="photo.urls.small"
        alt="photo.alt_description
"
      />
      <p>{{ photo.user.name }}</p>
    </div>
  </div>
</template>

<style scoped>
.nav {
  display: flex;
}

h1 {
  font-weight: bold;
  font-size: 30px;
}

.search {
  border: none;
  background-color: rgba(128, 128, 128, 0.1);
  padding: 10px;
  border-radius: 20px;
  margin-left: 50px;
}

.gallery {
  display: grid;
  grid-template-rows: repeat(auto-fill, minmax(200px, 1fr));
  gap: 10px;
}
.photo img {
  width: 100%;
  height: auto;
  border-radius: 5px;
}
</style>
