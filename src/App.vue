<template>
  <div>
    <div id="app" class="ml-2">
      <div>
        <label for="input-recipe-name">Название рецепта: </label>
        <input
          ref="inputRecipeName"
          id="input-recipe-name"
          type="text"
          placeholder="введите название"
          class="mt-2 mr-2"
          style="width: 12rem"
          v-model.trim="inputRecipeName"
          @keyup.stop.enter="addNewRecipe"
        />
        <button
          name="btnClearInput"
          v-if="inputRecipeName"
          class="h-6 w-6 text-white border-transparent bg-red-500 rounded-full"
          type="button"
          @click.stop=";(inputRecipeName = ''), $refs.inputRecipeName.focus()"
        >
          x
        </button>
        <label for="timeDay"> Период: </label>
        <select id="timeDay" required="true" v-model="timeDay">
          <option v-for="(timeDay, idx) in TIMES_DAY" :key="idx">
            {{ timeDay }}
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
      <div>
        <recipe-list
          v-for="recipe in recipeList"
          :recipe="recipe"
          :key="recipe.id"
          @el-selected="recipeSelected(recipe.id, recipeSelectedId)"
          @el-delete="recipeDelete(recipe)"
          class="pt-1 pb-1"
        />
      </div>
      <div v-if="recipeSelectedId">
        <hr class="mt-2 mb-2" />
        <recipe-details
          :recipe-components-list="currentRecipeComponentsList"
          @recipe-component-add="recipeComponentAdd"
        />
      </div>
    </div>
    <hr class="mt-2 mb-2" />
    <hr class="mt-2 mb-2" />
    <div :style="'text-align: center; font-weight: bold; width: ' + COUNT_DAY_VIEW * 12 + 'rem'">
      <h1>Меню недельное</h1>
      <button
        v-if="COUNT_DAY_VIEW > 1"
        @click="COUNT_DAY_VIEW -= 1"
        class="m-1 h-6 w-6 bg-red-500 text-white border-transparent rounded-full"
        type="button"
      >
        -
      </button>
      <span> {{ COUNT_DAY_VIEW }} (ДНЕЙ)</span>
      <button
        v-if="COUNT_DAY_VIEW < 7"
        @click="COUNT_DAY_VIEW += 1"
        class="m-1 h-6 w-6 bg-green-500 text-white border-transparent rounded-full"
        type="button"
      >
        +
      </button>
    </div>
    <hr class="mt-2 mb-2" />
    <div
      :style="'text-align: center'"
      v-for="(day, idx) in sliceDays(0, COUNT_DAY_VIEW)"
      :key="idx"
      class="column"
    >
      <h3 :style="day === 'СБ' || day === 'ВС' ? 'color: rgb(255, 0, 0)' : ''">
        {{ day }}
      </h3>
      <recipe-list-weak :recipe="recipeList" />
    </div>
  </div>
</template>

<script>
  import recipeAutoCompleat from './components/RecipeAutoComleat.vue'
  import recipeList from './components/RecipeList.vue'
  import recipeDetails from './components/RecipeDetails.vue'
  import recipeListWeak from './components/RecipeListWeak.vue'

  export default {
    name: 'App',
    components: {
      recipeAutoCompleat,
      recipeList,
      recipeDetails,
      recipeListWeak,
    },
    data() {
      return {
        DAYS_OF_WEAK: ['ПН', 'ВТ', 'СР', 'ЧТ', 'ПТ', 'СБ', 'ВС'],
        COUNT_DAY_VIEW: 1,
        TIMES_DAY: ['Завтрак', '2-ой Завтрак', 'Обед', 'Полдник', 'Ужин', 'Ужин-2'],
        timeDay: 'Завтрак',
        MIN_LEN_NAME: 0,
        inputRecipeName: '',
        recipeList: [],
        recipeCurrentId: 0,
        recipeSelectedId: null,
      }
    },
    mounted() {
      const recipeListData = localStorage.getItem('recipe-list')
      const recipeCurrentIdData = localStorage.getItem('recipe-current-id')
      const recipeSelectedIdData = localStorage.getItem('recipe-selected-id')
      if (recipeListData) {
        this.recipeList = JSON.parse(recipeListData)
        this.recipeCurrentId = JSON.parse(recipeCurrentIdData)
        this.recipeSelectedId = JSON.parse(recipeSelectedIdData)
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
          .find((recipe) => recipe.id === this.recipeSelectedId)
          ?.components.map((component) => component.name)
      },
      sizeTimesDay() {
        return this.TIMES_DAY.length
      },
    },
    methods: {
      toLocalStorageUpdate() {
        localStorage.setItem('recipe-list', JSON.stringify(this.recipeList))
        localStorage.setItem('recipe-current-id', JSON.stringify(this.recipeCurrentId))
        localStorage.setItem('recipe-selected-id', JSON.stringify(this.recipeSelectedId))
      },
      addNewRecipe() {
        if (this.inputRecipeName.length > this.MIN_LEN_NAME) {
          let size = this.sizeTimesDay - 1
          this.recipeList.push({
            id: (this.recipeCurrentId += 1),
            name: this.inputRecipeName,
            time: this.timeDay,
            selected: false,
            components: [],
          })
          this.inputRecipeName = ''
          if (this.recipeSelectedId) {
            this.recipeList.find((el) => el.id === this.recipeSelectedId).selected = false
            this.recipeSelectedId = null
          }
          this.toLocalStorageUpdate()
        }
      },
      recipeSelected(recipeId, recipeSelectedOldId) {
        if (recipeSelectedOldId)
          this.recipeList.find((el) => el.id === recipeSelectedOldId).selected = false
        this.recipeList.find((el) => el.id === recipeId).selected = true
        this.recipeSelectedId = recipeId
        this.toLocalStorageUpdate()
      },
      recipeDelete(recipeForDelete) {
        if (recipeForDelete.selected) {
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
      sliceDays(start = 0, count = 1) {
        return this.DAYS_OF_WEAK.slice(start, count)
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
