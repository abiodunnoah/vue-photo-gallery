<script setup>
defineProps(["photo", "isModalLoading"]);

const emit = defineEmits(["close"]);
</script>

<template>
  <div v-if="photo" class="modal" @click.self="emit('close')">
    <div class="modal-content">
      <button class="close-btn" @click.self="emit('close')">&times;</button>
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
  max-width: 80%;
  /* max-height: 90%; */
}
.modal img {
  width: 100%;
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

.spinner {
  height: 400px;
  /* background-color: rgb(27, 26, 26); */
  display: flex;
  justify-content: center;
  align-items: center;
}

@media (max-width: 670px) {
  .modal-content {
    background: white;
    padding: 20px;
    border-radius: 10px;
    text-align: center;
    max-width: 90%;
    max-height: 90%;
  }
  .modal img {
    width: 100%;
    max-width: 200px;
    /* height: 100%; */
    /* max-height: 300px; */
    border-radius: 8px;
  }
}
</style>
