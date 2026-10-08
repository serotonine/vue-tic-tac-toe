<script setup>
import { ref } from "vue";
const props = defineProps({
  id: { type: Number, required: true },
  currentPlayer: { type: Object },
});
const emit = defineEmits(["checked"]);
const symbol = ref(null);
const getSymbol = () => {
  symbol.value = props.currentPlayer.symbol;
  emit("checked", props.id);
};
</script>
<template>
  <div
    class="grid_item"
    :class="[`item-${id}`, { disabled: symbol }]"
    @click="getSymbol()"
  >
    {{ symbol && symbol }}
  </div>
</template>
<style scoped>
.grid_item {
  @apply border-slate-100 flex flex-col justify-center items-center text-9xl cursor-pointer;
  &.disabled {
    @apply pointer-events-none;
  }
}
.item-0,
.item-1,
.item-2 {
  @apply border-b-2;
}
.item-0,
.item-3,
.item-6 {
  @apply border-r-2;
}
.item-1,
.item-7 {
  @apply border-x-2;
}
.item-2,
.item-5,
.item-8 {
  @apply border-l-2;
}
.item-3,
.item-5 {
  @apply border-y-2;
}
.item-4 {
  @apply border-2;
}
.item-6,
.item-7,
.item-8 {
  @apply border-t-2;
}
</style>
