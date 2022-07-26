<script setup lang="ts">
import { computed } from "@vue/reactivity";
import { reactive, watch } from "vue";
import EditorOptions from "./components/EditorOptions.vue";
import ImageOptions from "./components/ImageOptions.vue";

const optionsArr = reactive([
  {
    index: 0,
    name: "Brightness",
    value: 100,
  },
  {
    index: 1,
    name: "Saturation",
    value: 100,
  },
  {
    index: 2,
    name: "Inversion",
    value: 0,
  },
  {
    index: 3,
    name: "Grayscale",
    value: 0,
  },
]);

const operation = reactive({
  rotate: 0,
  symmetry: false,
});

function resetFilters() {
  optionsArr.forEach((option, index) => {
    if (index === 2 || index === 3) {
      option.value = 0;
    } else {
      option.value = 100;
    }
  });
}
</script>

<template>
  <main class="editor__container">
    <h1 class="editor__title">Easy Image Editor</h1>
    <EditorOptions
      :operation="operation"
      :options="optionsArr"
      @resetFilters="resetFilters"
    ></EditorOptions>
    <ImageOptions :operation="operation" :options="optionsArr"></ImageOptions>
  </main>
</template>

<style lang="scss">
.editor {
  &__container {
    width: 90rem;
    padding: 2rem 4rem;
    background: #fff;
    border-radius: 5px;
    box-shadow: 0 4px 10px rgba($color: #000000, $alpha: 0.15);

    display: grid;
    grid-template-columns: 29rem 52rem;
    grid-template-rows: repeat(6, 7rem);
    gap: 1rem;
  }

  &__title {
    grid-column: 1 / 3;
    grid-row: 1 / 2;
    line-height: 7rem;
  }
}
</style>
