<template>
  <div class="index">
        <div class="index__button" @click="gotoFirst">К 1 лабораторной</div>
  </div>
</template>

<script setup lang="ts">
  import { useRouter } from "vue-router";
  const router = useRouter();
  function gotoFirst() {
    router.push({path: 'lab1'})
  };
</script>

<style scoped lang="scss">
  @import 'assets/css/main';

  .index {
    display: flex;
    padding: 20px 200px;
    gap: 20px;

    &__button {
      border: 1px solid $main-font;
      cursor: pointer;
      padding: 4px 12px;
      transition: 0.4s;

      &:hover {
        color: $main;
        border-color: $main;
        transition: 0.4s;
      }
    }
  }
</style>