<script setup>
import axios from "axios";
import { onMounted, ref, watch } from "vue";

const accessKey = import.meta.env.VITE_UNSPLASH_ACCESS_KEY;
const photos = ref(null);
const page = ref(1);
const search = ref("");
const selectedPhoto = ref(null);
const topics = ref(null);
const selectedTopic = ref("");

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

const selectTopic = (topicTitle) => {
  selectedTopic.value = topicTitle;
  search.value = topicTitle;
};

onMounted(() => {
  getPhotos();
  getTopic();
});
</script>
<!-- // how to make the active buttons disabled at search.value -->
<template>
  <nav>
    <div class="nav">
      <h1 class="header">Photos Gallery</h1>
      <input
        v-model.trim="search"
        type="search"
        placeholder="Search..."
        class="search"
      />
    </div>
    <div class="filter-container">
      <div class="filter">
        <NButton
          secondary
          type="info"
          class="buttons"
          @click="selectTopic('')"
          :class="{ active: selectedTopic === '' }"
          >All</NButton
        >
        <NButton
          secondary
          type="info"
          v-for="topic in topics"
          :key="topic.id"
          class="buttons"
          @click="selectTopic(topic.title)"
          :class="{ active: selectedTopic === topic.title }"
        >
          {{ topic.title }}</NButton
        >
      </div>
    </div>
  </nav>
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
  <div class="button-container">
    <button @click="page--">&lt;</button>
    <button @click="page++">></button>
  </div>

  <div v-if="selectedPhoto" class="modal" @click.self="closeModal">
    <div class="modal-content">
      <button class="close-btn" @click="closeModal">&times;</button>
      <img
        v-if="selectedPhoto && selectedPhoto.urls"
        :src="selectedPhoto.urls.regular"
        :alt="selectedPhoto.alt_description"
      />

      <p class="modal-text">
        {{ selectedPhoto.alt_description || "No title available" }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.nav {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin-top: 30px;
  margin-bottom: 30px;
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
  width: 100%;
  max-width: 400px;
}

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
.filter-container {
  width: 100%;
  overflow-x: auto;
  white-space: nowrap;
  scrollbar-width: none;
  -ms-overflow-style: none;
}
.filter-container::-webkit-scrollbar {
  display: none;
}

.filter {
  margin-bottom: 30px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-right: 20px;
  margin-left: 20px;
  /* gap: 5px; */
}
.buttons {
  cursor: pointer;
  margin-left: 8px;
  flex-shrink: 0;
  padding: 8px 16px;
  border-radius: 8px;
  transition: background 0.3s;
}
.buttons.active {
  background-color: #007bff; /* Highlight selected topic */
  color: white;
}
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-content {
  background: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  max-width: 90%;
  /* max-height: 90%; */
}
.modal img {
  width: 800%;
  max-width: 500px;
  border-radius: 8px;
}
.modal-text {
  margin-top: 10px;
  font-size: 18px;
}
.close-btn {
  position: absolute;
  top: 15px;
  right: 20px;
  font-size: 30px;
  background: none;
  border: none;
  color: white;
  cursor: pointer;
}
@media (max-width: 768px) {
  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  }

  .search {
    width: 90%;
  }
}

@media (max-width: 480px) {
  .nav {
    display: flex;
    flex-direction: column;
    justify-content: centers;
    align-items: center;
  }

  .header {
    font-size: 25px;
    margin-bottom: 10px;
  }

  .search {
    width: 80%;
    margin-left: 0;
  }

  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  }

  .photo {
    width: 100%;
  }

  .filter-container {
    width: 100%;
    overflow-x: auto;
    white-space: nowrap;
    scrollbar-width: none;
    -ms-overflow-style: none;
  }
  .filter-container::-webkit-scrollbar {
    display: block;
  }

  .filter {
    margin-bottom: 30px;
    display: flex;
    flex-direction: row;
    /* align-items: center;
    justify-content: space-between; */
    /* margin-right: 20px;
    margin-left: 20px; */
    /* flex-wrap: wrap; */
    /* gap: 5px; */
  }
}

@media (max-width: 1024px) {
  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  }

  .filter {
    flex-wrap: wrap;
  }
}

@media (min-width: 1200px) {
  .gallery {
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  }
}
</style>
