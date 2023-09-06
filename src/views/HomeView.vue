<template>
  <div class="flex h-screen">
    <div class="m-auto container">
      <div class="mockup-window border border-base-300" data-theme="aqua">
        <div class="flex justify-center px-4 py-16 border-t border-base-300">
          <table class="table text-lg">
            <thead class="text-xl">
              <tr>
                <th>#</th>
                <th>Name</th>
                <th>Price</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(product, index) in products" :key="product._id">
                <th>{{ index + 1 }}</th>
                <td>{{ product.name }}</td>
                <td>{{ product.price }}</td>
                <td>
                  <RouterLink :to="{ name: 'edit', params: { id: product._id } }" class="btn btn-outline btn-yellow">Edit
                  </RouterLink>
                  <button class="btn btn-primary"
                    style="margin-left: 2rem; border: 1px solid orangered; border-radius: 0.25rem;"
                    @click="destroy(product._id)">Delete</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted } from "vue";
const products = ref([]);
onMounted(async () => {
  products.value = await getAllProduct();
});
const API_PAHT = import.meta.env.VITE_API_PATH;
const getAllProduct = async () => {
  let data = [];
  data = await axios
    .get(`${API_PAHT}/getallproduct`)
    .then((response) => {
      return response.data;
    });
  return data;
};
const destroy = async (pro_id) => {
  await axios.delete(`${API_PAHT}/destory/${pro_id}`).then((response) => {
    window.location.reload();
  });
}
</script>


<style scoped>
.btn-yellow {
  background-color: yellow;
}

button:hover {
  color: aliceblue;
  background-color: orangered;
}
</style>
