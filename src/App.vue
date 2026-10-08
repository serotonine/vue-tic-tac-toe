<script setup>
import { ref, reactive } from "vue";
import Cell from "./components/Cell.vue";
import { WINNING_COMBINATIONS } from "./winning-combinaisons.js";

const players = reactive([
  { id: 1, name: "Fifi", symbol: "X", points: [], score: 0 },
  { id: 2, name: "Riri", symbol: "O", points: [], score: 0 },
]);
const NB_CELLS = 9;

// Ref. //
const currentPlayer = ref(players[0]);
const filledCells = ref(0);
const winner = ref(null);

// Init Cells. //
const cellKey = ref(0);

// Functions. //
const switchPlayer = () => {
  currentPlayer.value = currentPlayer.value.id === 1 ? players[1] : players[0];
};
const checkScore = () => {
  const { points } = currentPlayer.value;
  const hasWon = WINNING_COMBINATIONS.some((combinaison) => {
    return combinaison.every((c) => {
      return points.some((p) => p.row === c.row && p.column === c.column);
    });
  });
  if (hasWon) {
    winner.value = `${currentPlayer.value.name} has won!`;
    currentPlayer.value.score++;
    return true;
  }
  if (filledCells.value === NB_CELLS) {
    winner.value = "It's a draw!";
    return true;
  }
  return false;
};

// Event functions. //
/* on Cell click */
const addPoint = (index) => {
  if (winner.value) return;
  const cols = Math.sqrt(NB_CELLS);
  const row = Math.floor(index / cols);
  const column = index % cols;
  currentPlayer.value.points.push({ row, column });
  filledCells.value++;
  if (!checkScore()) {
    switchPlayer();
  }
};
/* on New game button click */
const setNewGame = () => {
  for (const player of players) {
    player.points = [];
  }
  winner.value = null;
  filledCells.value = 0;
  cellKey.value++;
};
</script>
<template>
  <div class="content">
    <section class="players container flex justify-between pb-10">
      <div
        v-for="player in players"
        :key="player.id"
        class="player"
        :class="currentPlayer.id === player.id && 'active'"
      >
        <p>{{ player.name }} : {{ player.symbol }}</p>
        <p>Score : {{ player.score }}</p>
      </div>
    </section>
    <section class="game container aspect-square">
      <div class="game_grid" :key="cellKey">
        <cell
          v-for="(_cell, index) in NB_CELLS"
          :key="index"
          :id="index"
          :currentPlayer="currentPlayer"
          @checked="addPoint"
        ></cell>
      </div>
    </section>
    <section
      v-if="winner"
      class="game_result m-10 text-white text-6xl text-center"
    >
      <p>{{ winner }}</p>
    </section>
    <section v-if="winner" class="m-10 text-white text-4xl text-center">
      <button class="btn btn-cta" type="button" @click="setNewGame()">
        One player shout again!
      </button>
    </section>
  </div>
</template>

<style scoped>
.content {
  @apply text-slate-50;
}
.container{
  @apply m-auto w-[85%] md:w-[75%] lg:w-[65%] xl:w-[45%] 2xl:w-[30%];
}
.game_grid {
  @apply grid grid-cols-3 grid-rows-3 w-full h-full;
}
.player {
  @apply tracking-wide antialiased;
  &.active {
    @apply text-yellow-200 font-bold;
  }
}
.btn {
  @apply text-white font-semibold antialiased rounded-md;
  &.btn-tag {
    @apply bg-blue-500 hover:bg-blue-700 py-1 px-2 text-xs md:text-sm;
  }
  &.btn-cta {
    @apply bg-blue-400 hover:bg-blue-500 py-2 px-4;
    &:disabled {
      @apply opacity-50;
    }
  }
  &.btn-cta-movie {
    @apply bg-blue-400 hover:bg-blue-500 p-2 aspect-square rounded-full;
  }
}
</style>
