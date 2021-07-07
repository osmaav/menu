<template>
  <span
    @mouseover.stop="onMouseOver"
    @mouseleave.stop="onMouseLeave(recipe, $event)"
    @click.stop="onClick(name)"
  >
    {{name}}
  </span>
</template>
<script>
  export default {
    name: 'recipeAutocompleat',
    props: {
      name: {name: ''},
    },
    emits: ['elOnClick'],
    methods: {
      onMouseOver(e) {
        e.target.style = 'color: black'
      },
      onMouseLeave(i, e) {
        e.target.style = 'color: white'
      },
      onClick(i) {
        this.$emit('elOnClick', i)
      },
    },
  }
</script>
