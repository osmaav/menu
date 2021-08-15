<template>
  <div class="card container" v-for="(recipe, idx) in recipe" :key="idx">
    <h4>{{ recipe?.time }}</h4>
    <h3>{{ recipe?.name }}</h3>
    <p v-for="(component, idx) in recipe?.components" :key="idx">
      {{ component.name }}
    </p>
  </div>
</template>

<script>
  export default {
    name: 'recipeListWeak',
    props: {
      recipe: {
        type: Object,
        require: true,
        id: Number,
        name: String,
        time: String,
        selected: Boolean,
        components: Array,
      },
    },
    computed: {
      recipeComponentsList() {
        return this.recipe?.components
      },
    },
  }
</script>

<style scoped>
  h4,
  h3,
  p {
    margin: 0;
    padding: 0;
  }
  h4 {
    color: rgb(127, 11, 243);
  }
  h3,
  h4 {
    font-weight: bold;
  }
</style>
