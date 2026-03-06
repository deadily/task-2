<template>
  <div class="card">
    <h4>{{ card.title }}</h4>
    <div class="card-items">
      <label v-for="(item, index) in card.items" :key="index" class="checkbox-item">
        <input
          type="checkbox"
          v-model="item.completed"
          @change="updateProgress"
          :disabled="isLocked"
        />
        <span :class="{ completed: item.completed }">{{ item.text }}</span>
      </label>
    </div>
    <p v-if="card.completedAt" class="completed-date">Завершено: {{ card.completedAt }}</p>
  </div>
</template>


<script>

</script>
