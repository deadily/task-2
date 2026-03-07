<template>
  <div class="card">
    <h4>{{ card.title }}</h4>
    <div class="card-items">
      <div v-for="(item, index) in card.items" :key="index" class="item-row">
        <select 
          v-model="item.status" 
          @change="updateProgress"
          :disabled="isLocked"
          class="status-select"
        >
          <option value="pending">o</option>
          <option value="completed">+</option>
          <option value="skipped">-</option>
        </select>
        <span :class="getItemClass(item)">{{ item.text }}</span>
      </div>
    </div>
    <p v-if="card.completedAt" class="completed-date">
      {{ card.completedAt }}
    </p>
  </div>
</template>


<script>
export default {
  name: 'Card',
  props: {
    card: Object,
    isLocked: Boolean,
  },
  emits: ['updateItems'],
  setup(props, { emit }) {
    const updateProgress = () => {
      emit('updateItems', props.card);
    };

    const getItemClass = (item) => {
      if (item.status === 'completed') return 'completed';
      if (item.status === 'skipped') return 'skipped';
      return '';
    };

    return {
      updateProgress,
      getItemClass
    };
  },
};

</script>
