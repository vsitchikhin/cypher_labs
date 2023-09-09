<template>
  <div class="index container">
    <div class="index__inputs-container">
      <label for="key" class="index__input-label">
        Введите ключ шифрования
        <input v-model="key" placeholder="Введите ключ" type="text" class="index__input" id="key">
      </label>
      <label for="phrase" class="index__input-label">
        Введите строку для шифрования
        <input v-model="originalPhrase" placeholder="Введите фразу" type="text" class="index__input" id="phrase">
      </label>
      <label for="checkbox" class="index__input-label index__input-label--row">
        <input v-model="clearSpaces" type="checkbox" class="index__checkbox" id="checkbox">
        Удалить пробелы из исходной строки
      </label>
      <div class="index__buttons-block">
        <div class="index__button" @click="encrypt">
          Зашифровать
        </div>
        <div class="index__button" @click="clearKey">
          Очистить ключ
        </div>
        <div class="index__button" @click="clearPhrase">
          Очистить фразу
        </div>
      </div>
    </div>

    <div class="index__output">{{ resultPhrase }}</div>
  </div>
</template>

<script lang="ts">
import {defineComponent} from "vue";

export default defineComponent({
  setup() {
    const resultPhrase = ref('');

    const key = ref('');
    const originalPhrase = ref('');

    const clearSpaces = ref(false);

    function encrypt() {
      // Очищаем предыдущий результат шифрования
      resultPhrase.value = '';

      // Получаем порядок перестановок из ключа
      const sequence = getEncryptSequence();

      let message: string[] = [];

      // Если стоит флаг, очищаем исходную строку от пробелов
      if (clearSpaces.value) {
        message = originalPhrase.value.replace(/\s/g, "").split("");
      } else {
        message = originalPhrase.value.split("");
      }

      // Расчитываем кол-во колонок матрицы
      const numColumns = Math.ceil(message.length / sequence.length);

      const matrix = [];
      let index = 0;

      // Заполняем матрицу
      for (let i = 0; i < numColumns; i++) {
        matrix[i] = [];

        for (let j = 0; j < sequence.length; j++) {
          matrix[i][j] = message[index] || '';
          index++;
        }
      }

      // Шифруем исходную строку, путем объединения символов в столбцах
      for (let col of sequence) {
        for (let row of matrix) {
          resultPhrase.value += row[col]
        }
      }
    }

    function clearKey() {
      key.value = '';
    }

    function clearPhrase() {
      originalPhrase.value = '';
      resultPhrase.value = '';
    }

    function getEncryptSequence() {
      // Создаем массивы символов ключа
      let keySequence: number[] | string[] = key.value.trim().split('');
      let keySortedString = key.value.trim().split('');

      // Сортируем один из массивов по алфавиту
      keySortedString = keySortedString.sort((a, b) => a.toLowerCase().charCodeAt(0) - b.toLowerCase().charCodeAt(0));

      // Заменяем символы второго массива на индексы соотв. символов первого
      keySortedString.forEach((c, i) => {
        const index = keySequence.findIndex(ch => ch === c);
        keySequence[index] = i;
      })

      return keySequence;
    }

    return {
      resultPhrase,
      key,
      originalPhrase,
      clearSpaces,

      encrypt,
      clearKey,
      clearPhrase,
    }
  }
})

</script>

<style scoped lang="scss">
@import 'assets/css/main';

.index {
  display: flex;
  flex-direction: column;
  gap: 40px;
  justify-content: center;
  align-items: center;
  align-content: center;
  height: 100vh;

  &__inputs-container {
    display: flex;
    flex-direction: column;
    width: 60vw;
    gap: 20px;
  }

  &__input {
    padding: 8px 16px;
    background-color: $darken;
    color: $main-font;
    outline: 2px solid $secondary;
    border: none;
    width: 100%;
    transition: box-shadow 0.5s;
    border-radius: 4px;
    font-size: 16px;
    line-height: 20px;

    &:hover {
      box-shadow: 0 0 8px $darken;
      transition: box-shadow 0.5s;
    }

    &:focus,
    &:focus-visible,
    &:focus-within {
      outline: 2px solid $main;
      border: none;
    }
  }

  &__buttons-block {
    display: flex;
    justify-content: space-between;
    width: 100%;
  }

  &__button {
    border: 2px solid $main-font;
    border-radius: 4px;
    color: $main-font;
    font-size: 16px;
    font-weight: 600;
    line-height: 20px;
    padding: 8px 24px;
    transition: 0.3s;
    cursor: pointer;

    &:hover {
      border-color: $main;
      color: $main;
      transition: 0.3s
    }
  }

  &__checkbox {
    width: 20px;
    height: 20px;
    margin-top: 4px;
  }

  &__input-label {
    display: flex;
    flex-direction: column;
    gap: 8px;


    &--row {
      flex-direction: row;
      align-items: center;
    }
  }

  &__output {
    color: $secondary-font;
    font-size: 28px;
    font-weight: 400;
    line-height: 32px;
  }
}

</style>
