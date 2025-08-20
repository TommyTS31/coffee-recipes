<template>
  <div class="flex justify-center mt-5">
    <div
      class="shadow w-11/12 md:w-3/4 lg:w-1/4 bg-gray-50 font-sans font-medium p-4 rounded-md"
    >
      <div>
        <label class="block mb-2 text-sm font-medium">Email</label>
        <input
          type="text"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5 mb-2"
          placeholder="John"
          required
          v-model="email"
        />
      </div>
      <label class="block mb-2 text-sm font-medium">Password</label>
      <input
        type="password"
        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5"
        required
        v-model="password"
      />
      <button
        class="bg-blackbg p-1.5 rounded-md w-full text-white mt-2 hover:bg-black"
        @click="login"
      >
        Log In
      </button>
    </div>
  </div>
</template>

<script setup>
const email = ref("");
const password = ref("");
const client = useSupabaseClient();
const {
  data: { user },
} = await client.auth.getUser();

onMounted(() => {
  watchEffect(() => {
    if (user) {
      navigateTo("/");
    }
  });
});

const login = async () => {
  const { data, error } = await client.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  });
  if (error) {
    console.log("error", error);
  } else {
    reloadNuxtApp();
    navigateTo("/");
  }
};
</script>
