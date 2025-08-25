<template>
  <div
    class="flex justify-between md:grid md:grid-cols-3 bg-white border-b border-gray-300 w-full"
  >
    <div></div>
    <a href="/" class="text-center">
      <div class="p-3">
        <p
          class="font-title font-regular text-lg font-semibold text-black hover:text-blackbg"
        >
          coffee recipes.
        </p>
      </div>
    </a>
    <div class="text-white font-sans m-3" v-if="!isLoggedIn">
      <button class="bg-black hover:bg-blackbg px-3 py-1 rounded-md shadow-sm">
        Login
      </button>
    </div>
    <div
      class="text-white font-sans my-3 mx-1 md:m-3 space-x-0.5 md:space-x-3"
      v-if="isLoggedIn"
    >
      <a
        class="bg-black hover:bg-blackbg px-3.5 py-1.5 rounded-md shadow-sm"
        href="/new-recipe"
        >+ Add Recipe</a
      >
      <button class="text-blackbg hover:text-black px-3 py-1" @click="logout">
        Log Out
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
const route = useRoute();

const client = useSupabaseClient();
const {
  data: { user },
} = await client.auth.getUser();

const isLoggedIn = computed(() => {
  if (user) {
    return true;
  } else {
    return false;
  }
});

async function logout() {
  await client.auth.signOut();
  reloadNuxtApp();
  navigateTo("/login");
}
</script>
