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
      title="Доработать" 
      :cards="columns.rework" 
      @move-card="moveCard" 
      @update-card="updateCardInColumn"
      column-id="rework" 
      :max-cards="null"
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

export default {
  name: 'Board',
  components: { Column },
  setup() {
    const columns = reactive({
      todo: [],
      inProgress: [],
      rework: [],
      done: [],
    });

    const loadState = () => {
      const saved = localStorage.getItem('boardState');
      if (saved) {
        try {
          const state = JSON.parse(saved);
          columns.todo = state.todo || [];
          columns.inProgress = state.inProgress || [];
          columns.rework = state.rework || [];
          columns.done = state.done || [];
        } catch (e) {
          console.error('Error loading state:', e);
        }
      }
    };

    const saveState = () => {
      localStorage.setItem('boardState', JSON.stringify(columns));
    };

    const getCardProgress = (card) => {
      if (!card.items || card.items.length === 0) return 0;
      const completed = card.items.filter(i => i.status === 'completed').length;
      return completed / card.items.length;
    };

    const hasSkippedItems = (card) => {
      return card.items.some(i => i.status === 'skipped');
    };

    const addCard = (card) => {
      const newCard = {
        id: Date.now(),
        title: card.title,
        items: card.items.map(item => ({
          text: typeof item === 'string' ? item : item.text,
          status: 'pending' // pending, completed, skipped
        })),
      };
      
      columns.todo.push(newCard);
      saveState();
    };

    const updateCardInColumn = (updatedCard, columnId) => {
      const column = columns[columnId];
      const index = column.findIndex(c => c.id === updatedCard.id);
      if (index !== -1) {
        column[index] = updatedCard;
        
        const progress = getCardProgress(updatedCard);
        
        if (columnId === 'todo' && progress > 0.5) {
          if (columns.inProgress.length < 5) {
            moveCard(updatedCard, 'inProgress');
            return;
          }
        }
        
        saveState();
      }
    };

    const moveCard = (card, newColumnId) => {
      const oldCol = Object.keys(columns).find(key =>
        columns[key].some(c => c.id === card.id)
      );
      
      if (oldCol) {
        columns[oldCol] = columns[oldCol].filter(c => c.id !== card.id);
      }

      if (newColumnId === 'done' || newColumnId === 'rework') {
        card.completedAt = new Date().toLocaleString();
      }

      columns[newColumnId].push(card);
      
      if (oldCol === 'inProgress' && newColumnId === 'done') {
        const todoCardToMove = columns.todo.find(c => getCardProgress(c) > 0.5);
        if (todoCardToMove) {
          setTimeout(() => {
            moveCard(todoCardToMove, 'inProgress');
          }, 0);
        }
      }
      
      saveState();
    };

    onMounted(() => {
      loadState();
    });

    watch(columns, () => {
      saveState();
    }, { deep: true });

    return {
      columns,
      addCard,
      moveCard,
      updateCardInColumn,
    };
  },
};
</script>
