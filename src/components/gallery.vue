<script setup>
import { defineProps } from "vue";

const { photos } = defineProps(["photos"]);

const emit = defineEmits(["PhotoSelected"]);

const openModal = (image) => {
  emit("PhotoSelected", image);
};
</script>

<template>
  <div class="gallery">
    <div
      v-for="photo in photos"
      :key="photo.id"
      class="photo"
      @click="openModal(photo)"
    >
      <img
        :src="photo.urls.small"
        alt="photo.alt_description
      "
      />

      <div class="p-tags">
        <p class="title">{{ photo.alt_description || "No title available" }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  /* gap: 5px; */
  justify-content: center;
  padding: 1rem;
}

.photo {
  width: 200px;
  /* height: 250px; */
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  /* line-height: 50px; */
  cursor: pointer;
}

.photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 5px;
}

.p-tags {
  text-align: center;
}

/* @media (max-width: 768px) {
  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  }

  .search {
    width: 90%;
  }
} */

@media (max-width: 670px) {
  .gallery {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
    gap: 8px;
  }

  .photo {
    width: 100%;
  }
}

@media (max-width: 1024px) {
  .gallery {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 8px;
  }
}

@media (min-width: 1025px) {
  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  }
}
</style>
