<template>
  <Container>
    <div class="py-20" v-if="loading">
      <div class="text-center">
        <div role="status">
          <svg
            aria-hidden="true"
            class="inline w-12 h-12 text-gray-200 animate-spin dark:text-gray-600 fill-primary mt-16"
            viewBox="0 0 100 101"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
              fill="currentColor"
            />
            <path
              d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
              fill="currentFill"
            />
          </svg>
          <span class="sr-only">Loading...</span>
        </div>
      </div>
    </div>
    <div class="py-20" v-else>
      <div class="mb-12">
        <h1 class="text-3xl md:text-4xl font-bold pb-2">
          Bienvenue, {{ userData.first_name }} !
        </h1>
        <p class="text-gray-500">Qu'allez-vous manger aujourd'hui ?</p>
      </div>

      <div class="flex space-x-4 mb-8">
        <Button variant="secondary" as-child>
          <router-link to="/shop/categories">Toutes les catégories</router-link>
        </Button>
        <Button variant="secondary" as-child>
          <router-link to="/shop/foods">Tout les plats</router-link>
        </Button>
      </div>
      <CategoryCarousel
        title="Catégories à l'affiche"
        :items="featuredCategories"
      />
      <FoodCarousel title="Plats populaires" :items="popularFoods" />
      <FoodGrid title="Tous les plats" :items="allFoods" />
      <Button variant="secondary" as-child>
        <router-link to="/shop/foods">Voir tout les plats</router-link>
      </Button>
    </div>
  </Container>
</template>

<script setup>
import Container from "@/components/Container.vue";
import CategoryCarousel from "@/components/CategoryCarousel.vue";
import FoodCarousel from "@/components/FoodCarousel.vue";
import FoodGrid from "@/components/FoodGrid.vue";
import { Button } from "@/components/ui/button";
import { ref, onMounted } from "vue";

const loading = ref(false);

const userData = ref({});
const featuredCategories = ref([]);
const popularFoods = ref([]);
const allFoods = ref([]);

onMounted(async () => {
  loading.value = true;
  await fetch(`${import.meta.env.VITE_API}/user/me`, {
    method: "GET",
    headers: {
      Authorization: `Bearer ${localStorage.getItem("token")}`,
    },
  })
    .then((res) => res.json())
    .then((data) => {
      userData.value = data;
      console.log(data);
    });

  await fetch(`${import.meta.env.VITE_API}/categories`, {
    method: "GET",
    headers: {
      Authorization: `Bearer ${localStorage.getItem("token")}`,
    },
  })
    .then((res) => res.json())
    .then((data) => {
      // categories where the featured property is true and active is true and randomize the order
      featuredCategories.value = data
        .filter((category) => category.featured && category.active)
        .sort(() => 0.5 - Math.random());
      console.log(data);
    });

  await fetch(`${import.meta.env.VITE_API}/foods`, {
    method: "GET",
    headers: {
      Authorization: `Bearer ${localStorage.getItem("token")}`,
    },
  })
    .then((res) => res.json())
    .then((data) => {
      // foods where the featured property is true and active is true and randomize the order
      popularFoods.value = data
        .filter((food) => food.featured && food.active)
        .sort(() => 0.5 - Math.random());

      // foods where the active property is true and randomize the order, max 12
      allFoods.value = data
        .filter((food) => food.active)
        .sort(() => 0.5 - Math.random())
        .slice(0, 12);
      loading.value = false;
      console.log(data);
    });
});
</script>
