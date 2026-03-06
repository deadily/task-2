<template>
  <div class="column">
    <h3>{{ title }}</h3>
    <div class="card-list">
      <Card
        v-for="card in cards"
        :key="card.id"
        :card="card"
        @update-items="onUpdateItems"
        :is-locked="isLocked"
      />
    </div>
    <CardForm v-if="columnId === 'todo' && canAddCard && !isLocked" @submit="addNewCard" />
    <p v-if="isLocked" class="lock-message">Столбец заблокирован (В процессе заполнен)</p>
    <p v-if="columnId !== 'todo' && !canAddCard" class="limit-message">Достигнут лимит ({{ maxCards }})</p>
  </div>
</template>


<script>
import Card from './Card.vue';
import CardForm from './CardForm.vue';
import { computed } from 'vue';



</script>
