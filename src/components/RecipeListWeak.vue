<template>
  <div>
    <div class="card container" v-for="(recipe, idx) in recipe" :key="idx">
      <h4>{{ recipe?.time }}</h4>
      <h3>{{ recipe?.name }}</h3>
      <p v-for="(component, idx) in recipe?.components" :key="idx">
        {{ component.name }}
      </p>
    </div>
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
  .card {
    margin: 10px;
    width: 11rem;
    transition: 0.3s;
    border-radius: 7px;
    box-shadow: 0 1px 3px 1px rgba(0, 0, 0, 0.2);
  }
  .card:hover {
    box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.3);
    font-weight: bold;
    text-shadow: 1px 1px 1px rgba(0, 0, 0, 0.4);
  }
  .container {
    padding: 2px 2px;
    background-color: rgba(200, 200, 200, 0.3);
  }
  h4,
  h3,
  p {
    margin: 0;
    padding: 0;
  }
  h4 {
    color: rgb(127, 11, 243);
  }
</style>
