<template>
  <div class="board">
    <Column
      title="Нужно сделать"
      :cards="columns.todo"
      @add-card="addCard"
      @move-card="moveCard"
      @update-card="updateCardInColumn"
      column-id="todo"
      :max-cards="3"
      :in-progress-cards-length="columns.inProgress.length"
    />
    <Column
      title="В процессе"
      :cards="columns.inProgress"
      @move-card="moveCard"
      @update-card="updateCardInColumn"
      column-id="inProgress"
      :max-cards="5"
      :in-progress-cards-length="columns.inProgress.length"
    />
    <Column
      title="Готово"
      :cards="columns.done"
      @move-card="moveCard"
      column-id="done"
      :max-cards="null"
      :in-progress-cards-length="columns.inProgress.length"
    />
  </div>
</template>


<script>
import Column from './Column.vue';
import { reactive, onMounted, watch } from 'vue';



</script>
