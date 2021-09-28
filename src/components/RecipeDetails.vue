<template>
  <div>
    <label for="input-component-name">компонент: </label>
    <input
      id="input-component-name"
      type="text"
      placeholder="введите название"
      style="width: 12rem"
      class="mt-2 mr-2"
      v-model.trim="inputComponentName"
      @keyup.enter.stop="recipeComponentAdd()"
    />
    <button
      class="w-6 h-6 text-white border-transparent bg-green-500 rounded-full"
      type="button"
      @click.stop="recipeComponentAdd()"
    >
      +
    </button>
    <div style="margin-top: 7px">
      состав:
      <div
        v-for="(component, idx) in recipeComponentsList"
        :key="idx"
        class="card container detail"
      >
        {{ component }}
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'recipeDetails',
    props: {
      recipeComponentsList: { type: Array, require: true },
    },
    data() {
      return {
        inputComponentName: '',
      }
    },
    emits: {
      recipeComponentAdd: null,
    },
    methods: {
      recipeComponentAdd() {
        this.$emit('recipeComponentAdd', this.inputComponentName)
        this.inputComponentName = ''
      },
    },
  }
</script>
<style scoped></style>
