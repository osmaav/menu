<template>
  <div class="card container" v-for="(timeDay, idx) in TIMES_DAY" :key="idx">
    <!-- @click.stop="onClick(recipe?.id)" -->
    <h4>{{ timeDay }}</h4>
    <select id="recipeName">
      <option value="">выберите рецепт</option>
      <option v-for="(r, idx) in filteredRecipeName(recipe, timeDay)" :key="idx">
        {{ r }}
      </option>
    </select>
  </div>
</template>

<script>
  export default {
    name: 'recipeListWeak',
    props: {
      TIMES_DAY: Array,
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
    emits: {
      // elSelected: null,
    },
    methods: {
      filteredRecipeName(recipe, timeDay) {
        return recipe.filter((r) => r.time == timeDay).map((c) => c.name)
      },
      // onClick(num) {
      //   this.$emit('elSelected', num)
      // },
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
  .card.container {
    width: 11rem;
  }
</style>
