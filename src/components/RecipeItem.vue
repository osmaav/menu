<template>
  <div>
    <li
      @mouseover.stop="onMouseOver"
      @mouseleave.stop="onMouseLeave(recipe, $event)"
      @click.stop="onClick(recipe.id)"
      :style="recipe.selected ? 'color: red' : 'black'"
    >
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
    name: 'recipeItem',
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
      onMouseOver(e) {
        if (e.target.type === 'button') return
        e.target.style = 'color: blue'
      },
      onMouseLeave(recipe, e) {
        e.target.type != 'button'
          ? recipe.selected
            ? (e.target.style = 'color: red')
            : (e.target.style = 'color: black')
          : {}
      },
      onClick(id) {
        this.$emit('elSelected', id)
      },
      delItem(recipe) {
        this.$emit('elDelete', recipe)
      },
    },
  }
</script>
