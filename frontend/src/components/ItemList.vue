<template>
  <div class="container">
    <h1>Todo List</h1>
    <form @submit.prevent="addItem">
      <input v-model="newName" placeholder="New item" required />
      <button type="submit">Add</button>
    </form>

    <ul>
      <li v-for="item in items" :key="item._id">
        <input type="checkbox" v-model="item.done" @change="updateItem(item)" />
        <span :style="{ textDecoration: item.done ? 'line-through' : 'none' }">
          {{ item.name }}
        </span>
        <button @click="deleteItem(item._id)">✕</button>
      </li>
    </ul>
  </div>
</template>

<script>
import api from "@/services/api";

export default {
  data() {
    return {
      items: [],
      newName: "",
    };
  },
  async created() {
    const res = await api.get("/items");
    this.items = res.data;
  },
  methods: {
    async addItem() {
      const res = await api.post("/items", { name: this.newName });
      this.items.unshift(res.data);
      this.newName = "";
    },
    async updateItem(item) {
      await api.put(`/items/${item._id}`, { done: item.done });
    },
    async deleteItem(id) {
      await api.delete(`/items/${id}`);
      this.items = this.items.filter((i) => i._id !== id);
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: 2rem auto;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  align-items: center;
  margin: 0.5rem 0;
}
button {
  margin-left: auto;
}
</style>
