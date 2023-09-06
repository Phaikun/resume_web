<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router';
let name = ref(''), price = ref(0), routerName = ref(''), _id = ref('');
const router = useRouter()
const API_PAHT = import.meta.env.VITE_API_PATH;

onMounted(() => {
  routerName.value = router.currentRoute.value.name;
  _id.value = router.currentRoute.value.params?.id;
  console.log("routerName", routerName.value);
  console.log("_id", _id.value);
  if (routerName.value == "edit") {
    initEdit();
  }
});
const initEdit = async () => {
  const { data } = await axios.get(`${API_PAHT}/getproduct/${_id.value}`);
  name.value = data.data.name;
  price.value = data.data.price;
}
const submit = async () => {
  const req = {
    name: name.value, price: price.value
  };
  if (routerName.value == "create") {
    await axios.post(
      `${API_PAHT}/create`,
      req
    ).then((response) => {
      name.value = '';
      price.value = '';
    });
  } else {
    await axios.post(
      `${API_PAHT}/update/${_id.value}`,
      req
    ).then((response) => {
      router.push({ name: 'home', replace: true});
    });
  }
}
</script>

<template>
  <div class="card w-96 bg-base-100 shadow-xl">
    <!-- <img
            src="./src/img/55.jpg"
            alt="fruit"
          /> -->
    <div class="card-body">
      <input class="input input-bordered join-item" placeholder="กรอกชื่อสินค้า" v-model="name" />
      <input class="input input-bordered join-item" placeholder="รายละเอียดสินค้า" />
      <input class="input input-bordered join-item" placeholder="ราคาสินค้า" v-model="price" />
      <div class="card-actions justify-end">
        <button class="btn btn-primary" v-on:click="submit()">เพิ่มสินค้า</button>
      </div>
    </div>
  </div>
</template>

<style scoped></style>

