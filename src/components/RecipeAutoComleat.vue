<template>
  <span class="card" @click.stop="onClick(title)">
    {{ title }}
  </span>
</template>
<script>
  export default {
    name: 'recipeAutoCompleat',
    props: {
      title: String,
    },
    emits: ['elOnClick'],
    methods: {
      onClick(s) {
        this.$emit('elOnClick', s)
      },
    },
  }
</script>
<style scoped>
  .card {
    margin: 0.15rem;
    padding: 0.25rem;
    font-size: 12px;
    transition: 0.2s;
  }
  .card:hover {
    margin: 0.25rem;
    padding: 0.25rem;
    box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.3);
    font-size: 14px;
    font-weight: bold;
    color: rgb(8, 12, 255);
    text-shadow: 1px 1px 1px rgba(0, 0, 0, 0.4);
  }
</style>
