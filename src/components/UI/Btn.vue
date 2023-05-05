<template>
<button class="btn"><slot></slot></button>
</template>

<style lang="scss" scoped>
@import "@/assets/_variables.scss";
.btn {
  display: inline-flex;
  text-indent: 0;
  align-items: center;
  text-align: center;
  justify-content: center;
  padding: 16px 32px;
  font-size: 16px;
  font-weight: 700;
  border: none;
  border-radius: $radius;
  box-shadow: $shadow;
  color: white;
  background: $gr-yellow;
  background-size: 200% auto;
  text-decoration: none;
  transition: all $transition;

  &.danger {
    background: $danger;
    padding: 8px 16px;
  }
}
@media screen and (min-width: 1024px) {
  .btn:hover {
    background-position: right center;
  }
}
</style>
