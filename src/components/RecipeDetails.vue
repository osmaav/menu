<template>
  <div>
    <label for="inp-detail">компонент: </label>
    <input
      id="inp-detail"
      type="text"
      placeholder="введите название"
      style="width: 12rem"
      class="mt-2 mr-2"
      v-model.trim="inputRecipeDetail"
      @keyup.enter.stop="recipeComponentAdd()"
    />
    <button
      class="w-6 h-6 bg-green-400 text-white border-transparent bg-green-500 rounded-full"
      type="button"
      @click.stop="recipeComponentAdd()"
    >
      +
    </button>
    <div>
      состав: {{recipeComponentsList}}
    </div>
  </div>
</template>

<script>
  export default {
    name: 'recipeDetails',
      props: {
        recipeComponentsList: { type: Array, require: false },
      },
      data() {
        return {
          inputRecipeDetail: '',
        }
      },
      emits: {
        recipeComponentAdd: null,
      },
      methods: {
        recipeComponentAdd() {
          this.$emit('recipeComponentAdd', this.inputRecipeDetail)
          this.inputRecipeDetail = ''
        },
      },
  }
</script>
