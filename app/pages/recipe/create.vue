<template>
  <div class="flex justify-center mt-5">
    <div
      class="shadow w-11/12 md:w-3/4 lg:w-7/12 bg-white font-sans font-medium p-4 rounded-md border border-gray-300"
    >
      <div>
        <p class="font-sans font-semibold text-2xl mb-4">Add A New Coffee Recipe</p>
        <label class="block mb-2 text-sm font-medium">Recipe Name</label>
        <input
          type="text"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5 mb-2"
          placeholder="V60 Light Roast"
          v-model="recipe.meal_name"
          required
        />
      </div>
      <div>
        <label class="block mb-2 text-sm font-medium mt-2">Description</label>
        <textarea
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg block w-full p-2.5 mb-2"
          placeholder="Kenyan light roast recipe"
          v-model="recipe.description"
          required
        />
      </div>
      <div>
        <label class="block mb-2 text-sm font-medium mt-2">Coffee Roast</label>
        <ul class="flex flex-row flex-wrap justify-center w-full">
          <div class="flex grow flex-wrap">
            <li v-for="roast in roastChoices" class="m-0.5 grow w-1/5">
              <input
                type="radio"
                name="roasts"
                :id="roast"
                class="py-2 mx-1.5 w-5 hidden peer"
                :value="roast"
                v-model="recipe.roasts"
              />
              <label
                class="inline-flex items-center text-sm font-sans font-medium px-2 w-full h-full py-1.5 text-gray-500 bg-white border border-gray-200 rounded-md cursor-pointer peer-checked:border-blackbg peer-checked:text-blackbg hover:text-gray-600 hover:bg-gray-100"
                :for="roast.type"
              >
                <Icon
                  name="ph:coffee-bean-fill"
                  :style="roast.colour"
                  class="size-4 mx-2"
                />
                {{ roast.type }}
              </label>
            </li>
          </div>
        </ul>
      </div>
      <div>
        <label class="block mb-2 text-sm font-medium mt-2">Brewing Method</label>
        <div v-for="brewClass in brewMethodChoices">
          <label class="block mb-2 text-sm font-medium mt-2 text-gray-500">{{
            brewClass.type
          }}</label>
          <ul class="flex flex-row flex-wrap justify-center w-full">
            <li
              v-for="brewMethod in brewClass.brewers"
              class="flex m-0.5 w-1/4 flex-wrap"
            >
              <input
                type="radio"
                name="brewMethods"
                :id="brewMethod"
                :value="brewMethod"
                v-model="recipe.brewMethod"
                class="py-2 mx-1.5 w-5 hidden peer"
              />
              <label
                class="block text-sm font-sans font-medium px-2 mb-0.5 w-full h-full py-1.5 text-gray-500 bg-white border border-gray-200 rounded-md cursor-pointer peer-checked:border-blackbg peer-checked:text-blackbg hover:text-gray-600 hover:bg-gray-100"
                :for="brewMethod"
                >{{ brewMethod.type }}</label
              >
            </li>
          </ul>
        </div>
      </div>

      <div>
        <label class="block mb-2 text-sm font-medium text-gray-900 mt-2" for="file_input"
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
const brewMethodChoices = ref([
  {
    type: "Espresso",
    brewers: [{ type: "Espresso Machine", svg: "" }],
  },
  {
    type: "Pourover",
    brewers: [
      { type: "V60 Dripper", svg: "" },
      { type: "Chemex", svg: "" },
      { type: "Kalita Wave", svg: "" },
    ],
  },
  {
    type: "Immersion Brewers",
    brewers: [
      { type: "Clever Dripper", svg: "" },
      { type: "Hario Switch", svg: "" },
      { type: "Aeropress", svg: "" },
      { type: "French Press", svg: "" },
    ],
  },
  {
    type: "Other",
    brewers: [
      { type: "Moka Pot", svg: "" },
      { type: "Machine Dripper", svg: "" },
      { type: "Coffee Pod", svg: "" },
    ],
  },
]);
const roastChoices = ref([
  { type: "Dark Roast", colour: "color: #3D2B1F" },
  { type: "Medium Roast", colour: "color: #654321" },
  { type: "Light Roast", colour: "color: #B38B6D" },
  { type: "Omni Roast", colour: "color: #7F5112" },
]);

const recipe = ref({
  meal_name: "",
  description: "",
  roasts: "",
  brewMethod: "",
  extra: "",
  image_link: "",
});

const imagePath = ref("");
const localImage = ref("");

async function onChangeFile(event) {
  localImage.value = event.target.files[0];
}

async function uploadImage() {
  // Upload image
  const { data, error } = await client.storage
    .from("Meal Images")
    .upload("meals/" + localImage.value.name, localImage.value, {
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
  await uploadImage();

  const { error } = await client.from("recipe-list").insert(recipe.value);
  if (error) {
    console.log(error);
  } else {
    navigateTo("/");
  }
}
</script>
