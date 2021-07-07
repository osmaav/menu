<template>
  <!-- <h2>Меню недельное</h2>
  <div v-for="day in days" :key="day.idx" class="column">
    <h3 :style="(day == 'СБ' | day == 'ВС' )? 'color: rgb(255, 0, 0)' : ''">
      {{ day }}
    </h3>
    <Recipe :products="products" />
  </div> -->

  <div id="app" class="ml-2">
    <div>
      <label for="inp">название рецепта: </label>
      <input
        ref="recipeInput"
        id="inp"
        type="text"
        placeholder="введите название"
        style="width: 12rem"
        class="mt-2 mr-2"
        @keyup.enter="addNewItem"
        v-model.trim="inputElMenu"
      />
      <button
        class="h-6 w-6 bg-green-400 text-white border-transparent bg-green-500 rounded-full"
        type="button"
        @click.stop="addNewItem"
      >
        +
      </button>
      <div class="mt-3">
        <label v-if="recipeList?.length" style="color: white" id="placeholder">
          <recipe-auto-compleat
            v-for="(item, idx) in placeholderList"
            :name="item"
            :key="idx"
            @el-on-click=";(inputElMenu = item), $refs.recipeInput.focus()"
            class="border rounded mr-2 p-1 bg-gray-300"
          />
        </label>
      </div>
    </div>
    <hr class="mt-2 mb-2" />
    <ol>
      <recipe-item
        v-for="item in recipeList"
        :recipe="item"
        :key="item.id"
        @el-selected="selectedElMenu(item)"
        @el-delete="delElMenu(item)"
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
</template>

<script>
  import recipeItem from './components/RecipeItem2.vue'
  import recipeAutoCompleat from './components/RecipeAutoComleat.vue'
  import recipeDetails from './components/RecipeDetails.vue'

  export default {
    name: 'App',
    data() {
      return {
        daysOfWeak: ['ПН', 'ВТ', 'СР', 'ЧТ', 'ПТ', 'СБ', 'ВС'],
        // products: [
        //   {
        //     time: 'Завтрак',
        //     title: 'Каша',
        //     ingridients: ['крупа', 'вода', 'что-то еще...'],
        //   },
        //   {
        //     time: '2-ой Завтрак',
        //     title: 'Суп',
        //     ingridients: ['морковь', 'мясо'],
        //   },
        //   {
        //     time: 'Обед',
        //     title: 'Салат',
        //     ingridients: ['лук', 'укроп'],
        //   },
        //   {
        //     time: 'Полдник',
        //     title: 'Суп',
        //     ingridients: ['морковь', 'мясо'],
        //   },
        //   {
        //     time: 'Ужин',
        //     title: 'Суп',
        //     ingridients: ['морковь', 'мясо'],
        //   },
        //   {
        //     time: 'Ужин-2',
        //     title: 'Суп',
        //     ingridients: ['морковь', 'мясо'],
        //   },
        // ],
        inputElMenu: '',
        minLength: 0,
        recipeList: [],
        currentRecipeId: 0,
        recipeSelected: false,
        selectedElMenuId: null,
      }
    },
    mounted() {
      const recipeListData = localStorage.getItem('recipe-list')
      const currentrecipeIdData = localStorage.getItem('current-recipe-id')
      const recipeSelectedData = localStorage.getItem('recipe-selected')
      const selectedElMenuIdData = localStorage.getItem('selected-el-menu-id')
      if (recipeListData) {
        this.recipeList = JSON.parse(recipeListData)
        this.currentrecipeId = JSON.parse(currentrecipeIdData)
        this.recipeSelected = JSON.parse(recipeSelectedData)
        this.selectedElMenuId = JSON.parse(selectedElMenuIdData)
      }
    },
    methods: {
      toLocalStorageUpdate() {
        localStorage.setItem('recipe-list', JSON.stringify(this.recipeList))
        localStorage.setItem('current-recipe-id', JSON.stringify(this.currentRecipeId))
        localStorage.setItem('recipe-selected', JSON.stringify(this.recipeSelected))
        localStorage.setItem('selected-el-menu-id', JSON.stringify(this.selectedElMenuId))
      },
      addNewItem() {
        if (this.inputElMenu.length > this.minLength) {
          this.recipeList.push({
            id: (this.currentRecipeId += 1),
            name: this.inputElMenu,
            selected: false,
            components: [],
          })
          this.inputElMenu = ''
          this.recipeSelected = false
          this.selectedElMenuId = null
          this.toLocalStorageUpdate()
        }
      },
      selectedElMenu(recipe) {
        if (!this.recipeSelected) this.recipeSelected = true
        this.selectedElMenuId = recipe.id
        this.recipeList.forEach((el) =>
          el.id === recipe.id ? (el.selected = true) : (el.selected = false),
        )
        this.toLocalStorageUpdate()
      },
      delElMenu(recipe) {
        if (recipe.selected) {
          this.recipeSelected = false
          this.selectedElMenuId = null
        }
        this.recipeList = this.recipeList.filter((el) => el.id != recipe.id)
        this.toLocalStorageUpdate()
      },
      recipeComponentAdd(componentName) {
        let currentRecipe = this.recipeList.find((r) => r.id === this.selectedElMenuId)
        currentRecipe.components.push({
          id: currentRecipe.components.length,
          name: componentName,
        })
        this.toLocalStorageUpdate()
      },
    },
    computed: {
      placeholderList() {
        return this.recipeList
          .map((i) => i.name)
          .filter((i) => i.includes(this.inputElMenu))
          .sort((a, b) => (a < b ? -1 : 0))
          .filter((el, idx) => idx < 5)
      },
      currentRecipeComponentsList() {
        return this.recipeList
          .find((i) => i.id === this.selectedElMenuId)
          ?.components.map((c) => c.name)
      },
    },
    watch: {
      recipeList() {
        this.toLocalStorageUpdate()
      },
    },
    components: {
      recipeItem,
      recipeAutoCompleat,
      recipeDetails,
    },
  }
</script>

<style>
  * {
    box-sizing: border-box;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    /* text-align: center; */
    color: #2c3e50;
    background-color: rgba(aa, aa, aa, 0.2);
    padding: 0 0;
  }
  h2 {
    text-transform: uppercase;
  }
  h3 {
    margin: 0 0;
  }
  .column {
    float: left;
    width: 12rem;
  }
</style>
