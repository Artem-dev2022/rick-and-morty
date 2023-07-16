<template>
    <button class="manu__btn">
        <slot></slot>
      <span>Пункт</span>
    </button>
</template>

<script>

</script>

<style scoped>
.manu__btn {
    display: flex;
    flex-direction: column;
    align-items: center;
}

</style>