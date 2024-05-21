<template>
  <div class="flex items-center justify-center my-10">
    <ul class="flex flex-wrap gap-2 mx-28">
      <li
        v-for="number in pageNumber"
        :key="number"
        class="border-[1px] border-blue-400 h-7 w-7 text-blue-400 bg-white"
      >
        <span
          class="flex justify-center items-center mt-[0.5px] cursor-pointer"
          @click="emit('paginate', number)"
          >{{ number }}
        </span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { onMounted, ref, watch } from "vue";

const props = defineProps({
  characterPerPage: Number,
  totalCharacters: Number,
  currentPage: Number,
});

const emit = defineEmits(["paginate"]);

const pageNumber = ref(pages());

function pages() {
  let arrs = [];
  for (
    let i = 1;
    i <= Math.ceil(props.totalCharacters / props.characterPerPage);
    i++
  ) {
    arrs.push(i);
  }

  return arrs;
}

watch(
  () => props.totalCharacters,
  () => {
    pageNumber.value = pages();
  }
);
</script>
