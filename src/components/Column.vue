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

export default {
  name: 'Column',
  components: { Card, CardForm },
  props: {
    title: String,
    cards: Array,
    maxCards: [Number, null],
    columnId: String,
    inProgressCardsLength: Number,
  },
  emits: ['addCard', 'moveCard', 'updateCard'],
  setup(props, { emit }) {
    const canAddCard = computed(() => props.maxCards === null || props.cards.length < props.maxCards);


    const isLocked = computed(() => {
      if (props.columnId === 'todo') {
        const inProgressFull = props.inProgressCardsLength >= 5;
        const hasReadyCard = props.cards.some(card => {
          const total = card.items.length;
          const completed = card.items.filter(i => i.completed).length;
          return total > 0 && completed / total > 0.5;
        });
        return inProgressFull && hasReadyCard;
      }
      return false;
    });


    const addNewCard = (cardData) => {
      emit('addCard', cardData);
    };


    const onUpdateItems = (card) => {
      const total = card.items.length;
      const completed = card.items.filter(i => i.completed).length;


      if (total === 0) return;


      const progress = completed / total;


      if (props.columnId === 'todo') {
        if (progress === 1) {
          emit('moveCard', card, 'done');
        } else if (progress > 0.5) {
          if (props.inProgressCardsLength < 5) {
            emit('moveCard', card, 'inProgress');
          }
        }
        emit('updateCard', card, 'todo');
       
      } else if (props.columnId === 'inProgress') {
        if (progress === 1) {
          emit('moveCard', card, 'done');
        }
        emit('updateCard', card, 'inProgress');
       
      } else if (props.columnId === 'done') {
        emit('updateCard', card, 'done');
      }
    };


    return {
      canAddCard,
      isLocked,
      addNewCard,
      onUpdateItems,
    };
  },
};

</script>
