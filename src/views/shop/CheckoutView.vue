<template>
  <Container>
    <div class="py-20">
      <div class="lg:grid lg:grid-cols-7">
        <div class="col-span-2 lg:order-last">
          <div class="ml-8">
            <h2 class="text-2xl font-semibold mb-8">Votre panier</h2>
            <div class="flex justify-between">
              <h3>Nombre d'articles :</h3>
              <div>{{ cartTotalProducts }}</div>
            </div>
            <div class="flex justify-between">
              <h3>Total :</h3>
              <div>{{ cartTotalPrice.toFixed(2) }} €</div>
            </div>
          </div>
        </div>
        <div class="col-span-5 lg:order-first">hey</div>
      </div>
    </div>
  </Container>
</template>

<script setup>
import Loading from "@/components/Loading.vue";
import Container from "@/components/Container.vue";
import { ref, onMounted } from "vue";

const loading = ref(false);

const cart = ref([]);
const cartTotalProducts = ref(0);
const cartTotalPrice = ref(0);

const getCart = () => {
  loading.value = true;

  fetch(`${import.meta.env.VITE_API}/cart`, {
    method: "GET",
    headers: {
      Authorization: `Bearer ${localStorage.getItem("token")}`,
    },
  })
    .then((res) => res.json())
    .then((data) => {
      cart.value = data;
      for (let i = 0; i < data.length; i++) {
        cartTotalProducts.value += data[i].quantity;
        cartTotalPrice.value += data[i].food.price * data[i].quantity;
      }
      loading.value = false;
      console.log(data);
    });
};

onMounted(() => {
  getCart();
});
</script>
