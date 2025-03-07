<script setup>
import { ref, watch, onMounted, defineAsyncComponent } from "vue";
import axios from "axios";
import Nav from "./components/Nav.vue";

const Gallery = defineAsyncComponent(() => import("@/components/gallery.vue"));
const Modal = defineAsyncComponent(() => import("@/components/Modal.vue"));

const accessKey = import.meta.env.VITE_UNSPLASH_ACCESS_KEY;

const photos = ref(null);
const page = ref(1);
const selectedPhoto = ref(null);
const selectedTopic = ref("");
const search = ref("");
const topics = ref(null);

const getPhotos = async () => {
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
  }
};

const getTopic = async () => {
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
  }
};

// watch for search
watch(search, (newValue) => {
  if (newValue.trim() !== selectedTopic.value) {
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
  page.value = 1;
  getPhotos();
});

const openModal = (image) => {
  selectedPhoto.value = image;
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

    <Suspense>
      <template #default>
        <Gallery :photos="photos" @photoSelected="openModal" />
      </template>
      <template #fallback>
        <div class="cards">
          <NSpin size="large" />
        </div>
      </template>
    </Suspense>

    <div class="button-container">
      <button @click="page--">&lt;</button>
      <button @click="page++">></button>
    </div>

    <Suspense>
      <template #default>
        <Modal
          v-if="selectedPhoto"
          :photo="selectedPhoto"
          @close="closeModal"
        />
      </template>
      <template #fallback>
        <div class="cards">
          <NSpin size="small" />
        </div>
      </template>
    </Suspense>
  </main>
</template>
<style scoped>
.cards {
  height: 600px;
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
