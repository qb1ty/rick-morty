<script setup>
import axios from "axios";
import { onMounted, reactive, ref, watch } from "vue";

import Pagination from "./components/Pagination.vue";

const characters = ref([]);
const characterPerPage = ref(20);
const totalCharacters = ref(0);
const currentPage = ref(1);
const loading = ref(false);

const filter = reactive({
  name: "",
  status: "",
});

function onChangeName(value) {
  return (filter.name = value);
}

function onChangeStatus(value) {
  return (filter.status = value);
}

function handleSubmit(event) {
  if (
    !event.target.name.value.trim().length &&
    !event.target.status.value.trim().length
  ) {
    fetchCharacters();
  }

  onChangeName(event.target.name.value);
  onChangeStatus(event.target.status.value);
}

function changePage(page) {
  currentPage.value = page;
}

async function fetchCharacters() {
  const response = await axios.get(
    `https://rickandmortyapi.com/api/character/?page=${currentPage.value}&name=${filter.name}&status=${filter.status}`
  );

  characters.value = response.data.results;
  totalCharacters.value = response.data.info.count;
}

onMounted(async () => {
  loading.value = true;
  await fetchCharacters();
  loading.value = false;
});

watch(currentPage, () => {
  fetchCharacters();
});
watch(filter, async () => {
  await fetchCharacters();
});
</script>

<template>
  <div>
    <div v-if="!loading" class="flex items-center justify-center my-10">
      <form
        class="flex flex-col items-center"
        @submit.prevent="handleSubmit($event)"
      >
        <div class="flex gap-10">
          <input
            type="text"
            name="name"
            placeholder="Name"
            class="outline-none border-0 border-transparent rounded-md px-3 py-1 font-bold w-[200px]"
          />
          <select
            name="status"
            class="outline-none border-0 border-transparent rounded-md px-3 py-1 font-bold w-[200px]"
          >
            <option value="" selected>all</option>
            <option value="unknown">unknown</option>
            <option value="alive">alive</option>
            <option value="dead">dead</option>
          </select>
        </div>
        <button
          type="submit"
          class="bg-green-500 py-1 px-5 border-0 border-transparent rounded-md mt-10"
        >
          Применить
        </button>
      </form>
    </div>

    <div class="flex flex-wrap justify-center gap-10 my-10">
      <div v-if="loading" class="md:animate-spin">
        <img src="/loader.svg" alt="" class="h-8" />
      </div>
      <div
        v-for="character in characters"
        :key="character.id"
        class="flex justify-between bg-[#3c4046] border border-transparent rounded-2xl pr-5 w-[600px]"
      >
        <div>
          <img
            :src="character.image"
            alt="Image"
            class="h-[13rem] rounded-l-2xl"
          />
        </div>
        <div class="flex flex-col justify-between mr-auto my-2 ml-5">
          <span class="text-2xl text-white font-bold">{{
            character.name
          }}</span>
          <span
            class="flex items-center gap-2 text-white font-light text-lg -mt-2"
          >
            <div
              v-if="character.status === 'unknown'"
              class="border border-transparent rounded-full h-2 w-2 bg-gray-400"
            ></div>
            <div
              v-else-if="character.status === 'Dead'"
              class="border border-transparent rounded-full h-2 w-2 bg-red-700"
            ></div>
            <div
              v-else-if="character.status === 'Alive'"
              class="border border-transparent rounded-full h-2 w-2 bg-green-500"
            ></div>

            {{ character.status }} - {{ character.species }}
          </span>

          <span class="text-[#787f7b] mt-3">Last known location:</span>
          <span class="-mt-1 text-white font-extralight">{{
            character.origin.name
          }}</span>

          <span class="text-[#787f7b] mt-3">First seen in:</span>
          <span class="-mt-1 text-white font-extralight">{{
            character.location.name
          }}</span>
        </div>
      </div>
    </div>
    <Pagination
      v-if="!loading"
      :character-per-page="characterPerPage"
      :total-characters="totalCharacters"
      @paginate="changePage"
    />
  </div>
</template>
