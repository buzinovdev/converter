<template>
  <form class="form" @submit.prevent>
    <slot></slot>
  </form>
</template>

<style scoped>
.form {
  width: 100%;
  max-width: 640px;
}
</style>
