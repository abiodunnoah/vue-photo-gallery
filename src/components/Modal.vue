<script setup>
import { saveAs } from "file-saver";

const props = defineProps(["photo", "isModalLoading"]);

const emit = defineEmits(["close"]);

const downloadImage = async () => {
  if (!props.photo || !props.photo.urls || !props.photo.urls.full) {
    console.error("Photo data is missing");
    return;
  }

  // try {
  //   const response = await fetch(props.photo.urls.full);
  //   const blob = await response.blob();
  //   const link = document.createElement("a");
  //   link.href = URL.createObjectURL(blob);
  //   link.download = "downloaded-image.jpg"; // Custom filename
  //   document.body.appendChild(link);
  //   link.click();
  //   document.body.removeChild(link);
  // } catch (error) {
  //   console.error("Error downloading image:", error);
  // }

  try {
    const response = await fetch(props.photo.urls.full);
    const blob = await response.blob();
    saveAs(blob, "download-image.jpg");
  } catch (error) {
    console.log("Error downloading image", error);
  }
};
</script>

<template>
  <div v-if="photo" class="modal" @click.self="emit('close')">
    <div class="modal-content">
      <button class="close-btn" @click="emit('close')">&times;</button>
      <div v-if="isModalLoading" class="spinner">
        <NSpin size="large" />
      </div>
      <div v-else>
        <img
          v-if="photo && photo.urls"
          :src="photo.urls.regular"
          :alt="photo.alt_description"
        />
      </div>

      <p class="modal-text">
        {{ photo.alt_description || "No title available" }}
      </p>
      <!-- <a :href="photo.urls.full" download target="_blank" class="download-btn"
        ><NButton secondary type="info">Download</NButton></a
      > -->
      <NButton secondary type="info" @click="downloadImage" class="download-btn"
        >Download</NButton
      >
    </div>
  </div>
</template>

<style scoped>
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
  max-width: 50%;
  /* max-height: 90%; */
}
.modal img {
  width: 70%;
  /* max-width: 500px; */
  border-radius: 8px;
}
.modal-text {
  margin-top: 10px;
  font-size: 18px;
}
.close-btn {
  position: absolute;
  top: 70px;
  right: 70px;
  font-size: 30px;
  background: none;
  border: none;
  color: white;
  cursor: pointer;
}

.spinner {
  height: 400px;
  /* background-color: rgb(27, 26, 26); */
  display: flex;
  justify-content: center;
  align-items: center;
}

.download-btn {
  margin-top: 10px;
}

@media (max-width: 670px) {
  .modal-content {
    background: white;
    padding: 20px;
    border-radius: 10px;
    text-align: center;
    max-width: 70%;
    /* max-height: 90%; */
  }
  .modal img {
    width: 70%;
    /* max-width: 200px; */
    border-radius: 8px;
  }
  .spinner {
    height: 200px;
  }
}
</style>
