<script setup lang="ts">
import { ref } from "vue";

interface Option {
  index: number;
  name: string;
  value: number;
}

interface Operation {
  rotate: number;
  symmetry: boolean;
}

const emit = defineEmits<{
  (e: "resetFilters"): void;
}>();

const props = defineProps<{
  options: Option[];
  operation: Operation;
}>();

const btnIndex = ref(0);

function reset() {
  emit("resetFilters");
}

function arrowLeft() {
  props.operation.rotate--;
}

function arrowRight() {
  props.operation.rotate++;
}
</script>

<template>
  <div class="editor__options">
    <div class="options__control">
      <h3 class="options__title">Filters</h3>
      <div class="options__content">
        <button
          v-for="btnInfo in props.options"
          :key="btnInfo.name"
          class="options__btn"
          :class="{ active: btnIndex === btnInfo.index }"
          @click="btnIndex = btnInfo.index"
        >
          {{ btnInfo.name }}
        </button>
      </div>
      <div class="options__process">
        <div class="options__info">
          <p class="options__name">{{ props.options[btnIndex].name }}</p>
          <p class="options__value">{{ props.options[btnIndex].value }}%</p>
        </div>
        <input
          type="range"
          name=""
          id=""
          v-model="props.options[btnIndex].value"
          min="0"
          max="200"
          class="options__input"
        />
      </div>
      <div class="options__rotate">
        <h3 class="options__title">Rotate & Flip</h3>
        <div class="options__content-2">
          <button class="options__btn-2" @click="arrowLeft">
            <font-awesome-icon icon="fa-solid fa-arrow-rotate-left" />
          </button>
          <button class="options__btn-2" @click="arrowRight">
            <font-awesome-icon icon="fa-solid fa-arrow-rotate-right" />
          </button>
          <button
            class="options__btn-2"
            @click="props.operation.symmetry = !props.operation.symmetry"
          >
            <font-awesome-icon icon="fa-solid fa-hand-middle-finger" />
          </button>
        </div>
      </div>
    </div>
    <button class="options__reset" @click="reset">Reset filters</button>
  </div>
</template>

<style lang="scss">
.editor {
  &__options {
    grid-column: 1 / 2;
    grid-row: 2 / 7;
    border-radius: 1rem;
  }
}

.options {
  &__control {
    height: 80%;
    border: 1px solid #eee;
    padding: 0 2rem;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
  }

  &__title {
    font-size: 1.6rem;
    font-weight: 300;
  }

  &__content {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;

    &-2 {
      display: flex;
      justify-content: space-between;
    }
  }

  &__btn {
    width: 12rem;
    height: 4rem;
    background-color: transparent;
    padding: 1rem 2rem;
    border: 1px solid rgb(181, 181, 181);
    border-radius: 5px;
    margin-bottom: 1rem;
    color: rgb(181, 181, 181);
    cursor: pointer;
    transition: all 0.5s;

    &-2 {
      width: 8rem;
      height: 4rem;
      background-color: transparent;
      padding: 1rem 2rem;
      border: 1px solid rgb(181, 181, 181);
      border-radius: 5px;
      font-size: 1.2em;
      cursor: pointer;
      color: rgb(94, 94, 94);
      transition: all 0.2s;

      &:active {
        transform: scale(1.05);
      }
    }

    &.active {
      border-color: #2ecc71;
      background-color: #2ecc71;
      color: #fff;
    }
  }

  &__info {
    display: flex;
    height: 4rem;
    align-items: center;
    justify-content: space-between;

    p {
      font-size: 1.4rem;
    }
  }

  &__reset {
    width: 14rem;
    height: 4rem;
    background-color: transparent;
    padding: 1rem 2rem;
    border: 1px solid rgb(181, 181, 181);
    border-radius: 2px;
    font-size: 1.2em;
    cursor: pointer;
    margin-top: 2rem;
    font-size: 1.6rem;
    color: rgb(94, 94, 94);
  }
}

input[type="range"] {
  -webkit-appearance: none;
  width: 24rem;
  height: 1rem;
  border-radius: 0.5rem; /*将轨道设为圆角的*/
  box-shadow: inset 0 0.125em 0.125em #343a3b7c;
  border-radius: 10px; /*这个属性设置使填充进度条时的图形为圆角*/
}
input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;

  height: 1.8rem;
  width: 1.8rem;
  border-radius: 50%;
  background: #2ecc71;
  cursor: pointer;
}
</style>
