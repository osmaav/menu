<template>
  <div>
    <li @click.stop="onClick(recipe.id)" :style="recipe.selected ? 'color: red' : 'black'">
      {{ recipe.name }}
      {{ recipe.time }}
      <div v-if="false">
        {{ recipe }}
      </div>
      <button
        class="h-6 w-6 bg-red-400 text-white border-transparent bg-red-500 rounded-full"
        type="button"
        @click.stop="delItem(recipe)"
      >
        -
      </button>
    </li>
  </div>
</template>
<script>
  export default {
    name: 'recipeList',
    props: {
      recipe: {
        require: true,
        id: 0,
        name: '',
        time: '',
        selected: false,
        components: [{}],
      },
    },
    emits: {
      elSelected: null,
      elDelete: null,
    },
    methods: {
      onClick(id) {
        this.$emit('elSelected', id)
      },
      delItem(recipe) {
        this.$emit('elDelete', recipe)
      },
    },
  }
</script>
<style scoped>
  li {
    transition: 0.2s;
  }
  li:hover {
    font-weight: bold;
    color: rgb(8, 12, 255);
    text-shadow: 1px 2px 3px rgba(0, 0, 0, 0.3);
  }
</style>
