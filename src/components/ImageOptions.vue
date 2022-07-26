<script setup lang="ts">
import { computed } from "@vue/reactivity";
import { onMounted, ref, watch } from "vue";

interface Option {
  index: number;
  name: string;
  value: number;
}

interface Operation {
  rotate: number;
  symmetry: boolean;
}

const props = defineProps<{
  options: Option[];
  operation: Operation;
}>();

const operationImage = ref<HTMLImageElement | null>(null);

onMounted(() => {
  operationImage.value!.style.height = "100%";
  operationImage.value!.style.width = "";
});

const rotateDeg = computed(() => {
  if (props.operation.rotate / 4 < 1) {
    return props.operation.rotate;
  } else {
    return props.operation.rotate % 4;
  }
});

watch(
  () => props.operation.rotate,
  () => {
    if (+props.operation.rotate % 2 === 0) {
      operationImage.value!.style.height = "100%";
      operationImage.value!.style.width = "";
    } else {
      operationImage.value!.style.width = "100%";
      operationImage.value!.style.height = "";
    }
    operationImage.value!.style.transform = `rotate(${
      rotateDeg.value * 90
    }deg) scale(${props.operation.symmetry ? "-1, 1" : "1, 1"})`;
  }
);

watch(
  () => props.operation.symmetry,
  (val) => {
    operationImage.value!.style.transform = `rotate(${
      rotateDeg.value * 90
    }deg) scale(${val ? "-1, 1" : "1, 1"})`;
  }
);

const fileInput = ref<HTMLInputElement | null>(null);
const choose = ref<HTMLButtonElement | null>(null);
const save = ref<HTMLButtonElement | null>(null);

function loadImage() {
  if (fileInput.value?.files?.length! > 0) {
    let file = fileInput.value!.files![0];
    console.log(file);

    let url = URL.createObjectURL(file);
    console.log(url);
    operationImage.value?.setAttribute("src", url);
  } else {
    return;
  }
}

async function saveImage() {
  const canvas = document.createElement("canvas");
  const ctx = canvas.getContext("2d");
  canvas.width = operationImage.value!.getClientRects()[0].width;
  canvas.height = operationImage.value!.getClientRects()[0].height;

  ctx!.filter = `brightness(${props.options[0].value / 100}) saturate(${
    props.options[1].value
  }%) invert(${props.options[2].value}%) grayscale(${props.options[3].value}%)`;

  ctx!.translate(canvas.width / 2, canvas.height / 2);

  if (rotateDeg.value !== 0) {
    ctx!.rotate((rotateDeg.value * Math.PI) / 2);
  }

  ctx!.scale(props.operation.symmetry ? -1 : 1, 1);

  operationImage.value?.setAttribute("crossOrigin", "anonymous");

  ctx!.drawImage(
    operationImage.value!,
    -canvas.width / 2,
    -canvas.height / 2,
    canvas.width,
    canvas.height
  );

  // document.querySelector("#app")!.appendChild(canvas);
  // console.log(canvas.toDataURL());

  const link = document.createElement("a");
  link.download = "image.jpg";
  console.log(canvas.toDataURL());
  link.href = await canvas.toDataURL();
  link.click();
}

onMounted(() => {
  choose.value!.addEventListener("click", () => {
    fileInput.value?.click();
  });

  fileInput.value?.addEventListener("change", () => {
    loadImage();
  });

  save.value?.addEventListener("click", () => {
    console.log("save btn clicked");
    saveImage();
  });
});
</script>

<template>
  <div class="editor__cover">
    <div class="editor__image">
      <img
        ref="operationImage"
        :style="{
          filter: `brightness(${props.options[0].value / 100}) saturate(${
            props.options[1].value
          }%) invert(${props.options[2].value}%) grayscale(${
            props.options[3].value
          }%)`,
        }"
        src="https://images.unsplash.com/photo-1658434228734-3876f9aba10d?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=987&q=80"
        alt=""
      />
    </div>
    <div class="editor__operations">
      <input
        type="file"
        class="editor__input"
        accept="image/*"
        ref="fileInput"
        hidden
      />
      <button class="editor__choose" ref="choose">Choose Image</button>
      <button class="editor__save" ref="save">Save Image</button>
    </div>
  </div>
</template>

<style lang="scss">
.editor {
  &__cover {
    grid-column: 2 / 3;
    grid-row: 2 / 7;
  }

  &__image {
    width: 100%;
    height: 80%;
    overflow: hidden;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  &__operations {
    margin-top: 2rem;
    display: flex;
    gap: 2rem;
    justify-content: flex-end;

    button {
      width: 15rem;
      height: 4rem;
      border: none;
      padding: 1rem;
      border-radius: 5px;
      font-size: 1.2em;
      cursor: pointer;
      color: #fff;
      font-size: 1.4rem;
    }
  }

  &__choose {
    background-color: rgb(58, 58, 58);
  }

  &__save {
    background-color: #2ecc71;
  }
}
</style>
