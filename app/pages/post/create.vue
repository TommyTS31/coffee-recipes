<template>
  <div class="flex justify-center mt-5">
    <div
      class="shadow w-11/12 md:w-3/4 lg:w-7/12 bg-white font-sans font-medium p-4 rounded-md border border-gray-300"
    >
      <div class="mb-4">
        <p class="font-sans font-semibold text-2xl mb-4">New Coffee Recipe</p>
        <label class="block mb-2 text-sm font-medium">Recipe Name</label>
        <input
          type="text"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5 mb-2"
          placeholder="V60 Light Roast"
          v-model="recipe.title"
          required
        />
      </div>
      <div class="mb-4">
        <label class="block mb-2 text-sm font-medium mt-2">Description</label>
        <textarea
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5 mb-2"
          placeholder="Kenyan light roast recipe"
          v-model="recipe.description"
          required
        />
      </div>
      <div class="mb-4">
        <label class="block mb-2 text-xs md:text-sm font-medium mt-2"
          >Coffee Roast</label
        >
        <ul class="grid grid-cols-2 md:grid-cols-4 gap-1 w-full">
          <li v-for="roast in roastChoices" class="">
            <input
              type="radio"
              name="roasts"
              :id="roast.id"
              class="py-2 mx-1.5 w-5 hidden peer"
              :value="roast.id"
              v-model="recipe.roast_type"
            />
            <label
              class="inline-flex items-center text-base font-sans font-medium px-2 w-full h-full py-1.5 text-gray-600 bg-white border border-gray-200 rounded-md cursor-pointer peer-checked:border-blackbg peer-checked:text-blackbg hover:text-gray-600 hover:bg-gray-100"
              :for="roast.id"
            >
              <Icon
                name="ph:coffee-bean-fill"
                :style="roast.colour"
                class="size-5 mx-2"
              />
              {{ roast.type }}
            </label>
          </li>
        </ul>
      </div>
      <div class="mb-4">
        <label class="block mb-2 text-sm font-medium mt-2"
          >Brewing Method</label
        >
        <ul class="grid grid-cols-2 md:grid-cols-3 gap-1 w-full">
          <li v-for="brewMethod in brewMethodChoices" class="">
            <input
              type="radio"
              name="brewMethods"
              :id="brewMethod.id"
              :value="brewMethod.id"
              v-model="recipe.coffee_brewer"
              class="py-2 mx-1.5 w-5 hidden peer"
            />
            <label
              class="inline-flex items-center text-base font-sans font-medium px-2 mb-0.5 w-full h-full py-1.5 text-gray-600 bg-white border border-gray-200 rounded-md cursor-pointer peer-checked:border-blackbg peer-checked:text-blackbg hover:text-gray-600 hover:bg-gray-100"
              :for="brewMethod.id"
            >
              <Icon :name="brewMethod.svg" class="size-6 mx-2 md:mx0.5" />
              {{ brewMethod.brewer }}
            </label>
          </li>
        </ul>
      </div>

      <div>
        <label
          class="block mb-2 text-sm font-medium text-gray-900 mt-2"
          for="file_input"
          >Upload an Image</label
        >
        <input
          class="w-full text-blackbg font-medium text-sm bg-white border border-gray-300 file:cursor-pointer cursor-pointer file:border-0 file:py-2.5 file:px-4 file:mr-4 file:bg-blackbg file:hover:bg-black file:text-white rounded-md"
          id="file_input"
          type="file"
          @change="onChangeFile"
        />
      </div>

      <button
        class="bg-blackbg p-1.5 rounded-md w-full text-white mt-4 hover:bg-black"
        @click="submitMeal"
      >
        Create
      </button>
    </div>
  </div>
</template>

<script setup>
const client = useSupabaseClient();

const recipe = ref({
  title: "",
  description: "",
  roast_type: "",
  coffee_brewer: "",
});

const imagePath = ref("");
const localImage = ref("");

async function onChangeFile(event) {
  localImage.value = event.target.files[0];
}

async function uploadImage() {
  // Upload image
  const { data, error } = await client.storage
    .from("Coffee Pictures")
    .upload("Post/" + localImage.value.name, localImage.value, {
      cacheControl: "3600",
      upsert: false,
    });
  if (error) {
    console.log(error);
  } else {
    recipe.value.image_link = data.fullPath;
  }
}

async function submitMeal() {
  console.log(recipe);
  //await uploadImage();

  const { error } = await client.from("coffee_recipe").insert(recipe.value);
  if (error) {
    console.log(error);
  } else {
    navigateTo("/");
  }
}
</script>
