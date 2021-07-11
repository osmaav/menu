<template>
  <div id="app" class="ml-2">
    <div>
      <label for="inputRecipeName">Название рецепта: </label>
      <input
        ref="inputRecipeName"
        id="inputRecipeName"
        type="text"
        placeholder="введите название"
        class="mt-2 mr-2"
        style="width: 12rem"
        v-model.trim="inputRecipeName"
        @keyup.stop.enter="addNewRecipe"
      />
      <label for="selectNameOfTime">Период: </label>
      <select name="time" id="selectNameOfTime" required="true" v-model="nameOfTimeSelected">
        <option v-for="(nameOfTime, idx) in NAME_OF_TIMES" :key="idx">
          {{ nameOfTime }}
        </option>
      </select>
      <button
        class="h-6 w-6 bg-green-400 text-white border-transparent bg-green-500 rounded-full"
        type="button"
        @click.stop="addNewRecipe"
      >
        +
      </button>
      <div>
        <label v-if="recipeList?.length" id="placeHolder">
          <recipe-auto-compleat
            v-for="(recipe, idx) in placeHolderList"
            :name="recipe"
            :key="idx"
            @el-on-click=";(inputRecipeName = recipe), $refs.inputRecipeName.focus()"
            class="rounded bg-gray-200"
          />
        </label>
      </div>
    </div>
    <hr class="mt-2 mb-2" />
    <ol>
      <recipe-item
        v-for="recipe in recipeList"
        :recipe="recipe"
        :key="recipe.id"
        @el-selected="selectedElMenu(recipe)"
        @el-delete="recipeDelete(recipe)"
        class="pt-1 pb-1"
      />
    </ol>
    <div v-if="recipeSelected">
      <hr class="mt-2 mb-2" />
      <recipe-details
        :recipe-components-list="currentRecipeComponentsList"
        @recipe-component-add="recipeComponentAdd"
      />
    </div>
  </div>
  <hr class="mt-2 mb-2" />
  <hr class="mt-2 mb-2" />
  <h1 :style="'text-align: center'">Меню недельное</h1>
  <hr class="mt-2 mb-2" />
  <div :style="'text-align: center'" v-for="day in DAYS_OF_WEAK" :key="day.idx" class="column">
    <h3 :style="day === 'СБ' || day === 'ВС' ? 'color: rgb(255, 0, 0)' : ''">
      {{ day }}
    </h3>
    <recipe-list :recipes="recipeList" />
  </div>
</template>

<script>
  import recipeList from './components/RecipeList.vue'
  import recipeItem from './components/RecipeItem.vue'
  import recipeAutoCompleat from './components/RecipeAutoComleat.vue'
  import recipeDetails from './components/RecipeDetails.vue'

  export default {
    name: 'App',
    components: {
      recipeList,
      recipeItem,
      recipeAutoCompleat,
      recipeDetails,
    },
    data() {
      return {
        DAYS_OF_WEAK: ['ПН', 'ВТ', 'СР', 'ЧТ', 'ПТ', 'СБ', 'ВС'],
        NAME_OF_TIMES: ['Завтрак', '2-ой Завтрак', 'Обед', 'Полдник', 'Ужин', 'Ужин-2'],
        nameOfTimeSelected: 'Завтрак',
        MIN_LEN_NAME: 0,
        inputRecipeName: '',
        recipeList: [],
        recipeCurrentId: 0,
        recipeSelected: false,
        recipeSelectedId: null,
      }
    },
    mounted() {
      const recipeListData = localStorage.getItem('recipe-list')
      const recipeCurrentIdData = localStorage.getItem('recipe-current-id')
      const recipeSelectedIdData = localStorage.getItem('recipe-selected-id')
      const recipeSelectedData = localStorage.getItem('recipe-selected')
      if (recipeListData) {
        this.recipeList = JSON.parse(recipeListData)
        this.recipeCurrentId = JSON.parse(recipeCurrentIdData)
        this.recipeSelectedId = JSON.parse(recipeSelectedIdData)
        this.recipeSelected = JSON.parse(recipeSelectedData)
      }
    },
    computed: {
      placeHolderList() {
        return this.recipeList
          .map((i) => i.name)
          .filter((i) => i.includes(this.inputRecipeName))
          .sort((a, b) => (a < b ? -1 : 0))
          .filter((el, idx) => idx < 5)
      },
      currentRecipeComponentsList() {
        return this.recipeList
          .find((i) => i.id === this.recipeSelectedId)
          ?.components.map((c) => c.name)
      },
      sizeNameOfTimes() {
        return this.NAME_OF_TIMES.length
      },
    },
    methods: {
      toLocalStorageUpdate() {
        localStorage.setItem('recipe-list', JSON.stringify(this.recipeList))
        localStorage.setItem('recipe-current-id', JSON.stringify(this.recipeCurrentId))
        localStorage.setItem('recipe-selected', JSON.stringify(this.recipeSelected))
        localStorage.setItem('recipe-selected-id', JSON.stringify(this.recipeSelectedId))
      },
      addNewRecipe() {
        if (this.inputRecipeName.length > this.MIN_LEN_NAME) {
          let size = this.sizeNameOfTimes - 1
          this.recipeList.push({
            id: (this.recipeCurrentId += 1),
            name: this.inputRecipeName,
            time: this.nameOfTimeSelected,
            selected: false,
            components: [],
          })
          this.inputRecipeName = ''
          this.recipeSelected = false
          this.recipeSelectedId = null
          this.toLocalStorageUpdate()
        }
      },
      selectedElMenu(recipe) {
        if (!this.recipeSelected) this.recipeSelected = true
        this.recipeSelectedId = recipe.id
        this.recipeList.forEach((el) =>
          el.id === recipe.id ? (el.selected = true) : (el.selected = false),
        )
        this.toLocalStorageUpdate()
      },
      recipeDelete(recipeForDelete) {
        if (recipeForDelete.selected) {
          this.recipeSelected = false
          this.recipeSelectedId = null
        }
        this.recipeList = this.recipeList.filter((recipe) => recipe.id != recipeForDelete.id)
        this.toLocalStorageUpdate()
      },
      recipeComponentAdd(componentName) {
        let currentRecipe = this.recipeList.find((r) => r.id === this.recipeSelectedId)
        currentRecipe.components.push({
          id: currentRecipe.components.length,
          name: componentName,
        })
        this.toLocalStorageUpdate()
      },
    },
    watch: {
      recipeList() {
        this.toLocalStorageUpdate()
      },
    },
  }
</script>
<style scoped>
  h1,
  h3 {
    text-transform: uppercase;
    font-weight: bold;
  }
</style>
