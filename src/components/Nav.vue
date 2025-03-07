<script setup>
defineProps(["search", "topics", "selectedTopic"]);

const emit = defineEmits(["update:selectedTopic", "update:search"]);

const selectTopic = (topicId) => {
  emit("update:selectedTopic", topicId);
};
</script>

<template>
  <div>
    <nav>
      <div class="nav">
        <div class="header">
          <h1>Photos Gallery</h1>
        </div>
        <div class="input">
          <input
            :value="search"
            @input="$emit('update:search', $event.target.value)"
            type="search"
            placeholder="Search..."
            class="search"
          />
        </div>
        <div class="avatar">
          <img
            src="https://img.freepik.com/premium-psd/smiling-3d-cartoon-man_975163-762.jpg?w=826"
            alt="profile-image"
            class="profile-image"
          />
        </div>
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
            @click="selectTopic(topic.id)"
            :class="{ active: selectedTopic === topic.id }"
          >
            {{ topic.title }}</NButton
          >
        </div>
      </div>
    </nav>
  </div>
</template>

<style scoped>
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 30px;
  margin-bottom: 30px;
  margin-left: 20px;
  margin-right: 20px;
  max-width: 100%;
}

.avatar {
  width: 108px;
}

.profile-image {
  width: 40px;
  border-radius: 25px;
  margin-left: 50px;
}

h1 {
  font-weight: bold;
  font-size: 30px;
}

.search {
  border: none;
  background-color: rgba(128, 128, 128, 0.1);
  padding: 10px;
  padding-right: 200px;
  border-radius: 20px;
  /* width: 100%; */
  /* max-width: 400px; */
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
  background-color: #007bff;
  color: white;
}

@media (max-width: 670px) {
  .nav {
    display: flex;
    flex-direction: column;
    justify-content: centers;
    align-items: center;
  }

  .header h1 {
    font-size: 25px;
    margin-bottom: 10px;
  }

  .search {
    width: 80%;
    margin-left: 0;
  }

  .filter-container {
    width: 100%;
    display: flex;
    overflow-x: auto;
    white-space: nowrap;
    -ms-overflow-style: none;
    scrollbar-width: none;
  }
  .filter-container::-webkit-scrollbar {
    display: none;
  }
}
</style>
