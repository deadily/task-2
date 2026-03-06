<template>
  <form @submit.prevent="submitForm" class="card-form">
    <input
      v-model="title"
      placeholder="Заголовок карточки"
      required
      class="form-input"
    />
   
    <div class="items-container">
      <div v-for="(item, index) in items" :key="index" class="item-input-wrapper">
        <span class="item-number">{{ index + 1 }}</span>
        <input
          v-model="items[index]"
          :placeholder="`Пункт ${index + 1}`"
          class="form-input"
          required
        />
        <button
          v-if="items.length > 3"
          type="button"
          @click="removeItem(index)"
          class="remove-btn"
        >
          ×
        </button>
      </div>
    </div>


    <div class="form-actions">
      <button
        v-if="items.length < 5"
        type="button"
        @click="addItem"
        class="add-item-btn"
      >
        + Добавить пункт
      </button>
      <button type="submit" class="submit-btn">Добавить карточку</button>
    </div>
  </form>
</template>


<script>
import { ref } from 'vue';



</script>


<style scoped>


</style>