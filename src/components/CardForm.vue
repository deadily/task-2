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

export default {
  name: 'CardForm',
  emits: ['submit'],
  setup(_, { emit }) {
    const title = ref('');
    const items = ref(['', '', '']);


    const addItem = () => {
      if (items.value.length < 5) {
        items.value.push('');
      }
    };


    const removeItem = (index) => {
      if (items.value.length > 3) {
        items.value.splice(index, 1);
      }
    };


    const submitForm = () => {
      const filteredItems = items.value.filter(item => item.trim());
     
      if (filteredItems.length < 3) {
        alert("Заполните не менее 3-х пунктов");
        return;
      }


      emit('submit', {
        title: title.value,
        items: filteredItems,
      });


      title.value = '';
      items.value = ['', '', ''];
    };


    return {
      title,
      items,
      addItem,
      removeItem,
      submitForm,
    };
  },
};

</script>


<style scoped>


</style>