<template>
  <div
    class="card container"
    @click.stop="onClick(recipe.id)"
    :style="recipe.selected ? 'color: red' : 'black'"
  >
    {{ recipe.name }} "на"
    {{ recipe.time }}
    <div v-if="false">
      {{ recipe }}
    </div>
    <button
      class="h-4 w-4 text-white border-transparent bg-red-500 rounded-full"
      type="button"
      @click.stop="delItem(recipe)"
    >
      x
    </button>
  </div>
</template>
<script>
  export default {
    name: 'recipeList',
    props: {
      recipe: {
        type: Object,
        require: true,
        id: Number,
        name: String,
        time: String,
        components: Array,
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
