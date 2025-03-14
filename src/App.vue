<script setup>
import { ref, watch, onMounted } from "vue";
import axios from "axios";
import Nav from "./components/Nav.vue";
import Gallery from "@/components/gallery.vue";
import Modal from "@/components/Modal.vue";

// const Gallery = defineAsyncComponent(() => import("@/components/gallery.vue"));
// const Modal = defineAsyncComponent(() => import("@/components/Modal.vue"));

const accessKey = import.meta.env.VITE_UNSPLASH_ACCESS_KEY;

const photos = ref(null);
const page = ref(1);
const selectedPhoto = ref(null);
const selectedTopic = ref("");
const search = ref("");
const topics = ref(null);
const isLoading = ref(false);
const isModalLoading = ref(false);

const getPhotos = async () => {
  isLoading.value = true;
  try {
    let url = "https://api.unsplash.com/photos";

    if (selectedTopic.value) {
      url = `https://api.unsplash.com/topics/${selectedTopic.value}/photos`;
    }

    const endPoint = search.value.trim()
      ? "https://api.unsplash.com/search/photos"
      : url;

    const response = await axios.get(endPoint, {
      headers: {
        Authorization: `Client-ID ${accessKey}`,
      },
      params: {
        query: search.value.trim() || undefined,
        page: page.value,
        per_page: 28,
      },
    });
    photos.value = search.value.trim() ? response.data.results : response.data;
  } catch (error) {
    console.log("Error fetching Unsplash photos", error);
  } finally {
    isLoading.value = false;
  }
};

const getTopic = async () => {
  isLoading.value = true;
  try {
    const res = await axios.get("https://api.unsplash.com/topics", {
      headers: {
        Authorization: `Client-ID ${accessKey}`,
      },
      params: {
        per_page: 19,
      },
    });
    topics.value = res.data;
  } catch (error) {
    console.log("Error fetching topics", error);
  } finally {
    isLoading.value = false;
  }
};

// watch for search
watch(search, (newValue) => {
  if (newValue.trim() !== "" && newValue.trim() !== selectedTopic.value) {
    selectedTopic.value = "";
  }
  page.value = 1;
  getPhotos();
});

// watch for page
watch(page, () => {
  if (page.value > 0) {
    getPhotos();
  }
});

// watch for filter
watch(selectedTopic, () => {
  if (selectedTopic.value) {
    search.value = "";
  }
  page.value = 1;
  getPhotos();
});

const openModal = (image) => {
  isModalLoading.value = true;
  selectedPhoto.value = image;

  setTimeout(() => {
    isModalLoading.value = false;
  }, 2000);
};

const closeModal = () => {
  selectedPhoto.value = null;
};

onMounted(() => {
  getPhotos();
  getTopic();
});
</script>

<template>
  <main>
    <Nav
      v-model:search="search"
      :topics="topics"
      v-model:selectedTopic="selectedTopic"
    />

    <div v-if="isLoading" class="spinner">
      <NSpin size="large" />
    </div>

    <Gallery v-else :photos="photos" @photoSelected="openModal" />

    <div class="button-container">
      <button @click="page--">&lt;</button>
      <button @click="page++">></button>
    </div>

    <Modal
      v-if="selectedPhoto"
      :photo="selectedPhoto"
      @close="closeModal"
      :isModalLoading="isModalLoading"
    />
  </main>
</template>
<style scoped>
.spinner {
  height: 400px;
  /* background-color: rgb(27, 26, 26); */
  display: flex;
  justify-content: center;
  align-items: center;
}

.button-container {
  display: flex;
  justify-content: center;
  padding-top: 30px;
  margin-bottom: 50px;
}
.button-container button {
  border: none;
  width: 50px;
  height: 50px;
  border-radius: 100%;
  margin: 0 5px;
  cursor: pointer;
}
</style>
