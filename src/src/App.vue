<template>
  <div id="app">
    <div class="header">
      <img
        class="logo"
        src="@/assets/uoc-logo.png"
        alt="UOC"
        @click="recargar"
      />
      <div class="app-name">Recipe book</div>
    </div>
    <SearchBar v-on:showForm="toggleForm" v-on:search="setSearchTerm" />
    <RecipeList
      :recipeList="recipeListFiltered"
      v-on:deleteRecipe="deleteRecipe"
    />
    <RecipeForm
      v-on:addRecipe="addRecipe"
      v-on:closeModal="toggleForm"
      v-if="showModal"
    />
  </div>
  <div class="footer">
    <!-- peu de pàgina -->
    <p>Miguel Palanca Bassols - PAC4 - Programació web Avançada</p>
  </div>
</template>

<script>
import { defineComponent } from "vue";
import RecipeForm from "./components/RecipeForm.vue";
import RecipeList from "./components/RecipeList.vue";
import SearchBar from "./components/SearchBar.vue";
import axios from "axios";

export default defineComponent({
  name: "App",
  components: {
    RecipeList: RecipeList,
    RecipeForm,
    SearchBar,
  },
  data: () => ({
    recipeList: {},
    showModal: false,
    searchTerm: "",
  }),
  methods: {
    //mostrar receptes de manera asincrona de l'API
    async getRecetas() {
      const recipeList = await axios.get("http://localhost:3000/recipes"); //truca a l'API de les receptes per mostra-les
      this.recipeList = recipeList.data.recipes;
    },
    //Envia de manera asincrona la nova recepta.
    async addRecipe(recipe) {
      this.recipeList = [...this.recipeList, recipe];
      this.toggleForm();
      await axios.post("http://localhost:3000/recipe", recipe) //envia "recipe" amb la informació objecte amb les dades de la recepta
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      console.log("Nueva receta: " + recipe.title);
    },
    //Borra de manera asincrona recepta amb la ID seleccionada
    async deleteRecipe(recipeId) {
      this.recipeList = this.recipeList.filter(
        (recipe) => recipe.id !== recipeId
      );
      await axios.delete("http://localhost:3000/recipe", { data: { id: recipeId } }) //envia ID per borra la recepta
        .then(function (response) {
          console.log(response);
        })
        .catch(function (error) {
          console.log(error);
        });
      console.log("Receta borrada: " + recipeId);
    },
    //mostrar formulari
    toggleForm() {
      this.showModal = !this.showModal;
    },
    //busca si te alguna cosa escrita
    setSearchTerm(searchTerm) {
      this.searchTerm = searchTerm;
    },

    //recaga la web
    recargar() {
      window.location.reload();
    },
  },
  created() {
    this.getRecetas(); //truca a getRecetas per mostrar les receptes
  },
  computed: {
    //filtre de receptes
    recipeListFiltered() {
      if (!this.searchTerm) {
        return this.recipeList;
      }
      return this.recipeList.filter((recipe) => {
        return (
          recipe.title.toLowerCase().includes(this.searchTerm.toLowerCase()) ||
          recipe.ingredients.find((ingredient) =>
            ingredient.includes(this.searchTerm)
          )
        );
      });
    },
  },
});
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-wrap: wrap;
  height: 100%;
  width: 100%;
  max-width: 1280px;
  margin: 0 auto;
}
.header {
  width: 100%;
  padding: 15px;
  display: flex;
  align-items: center;
  border-bottom: 1px solid #ccc;
}
.header .logo {
  max-height: 50px;
}
.header .app-name {
  margin-left: 25px;
  font-weight: bold;
  font-size: 20px;
}
.footer {
  border-top: 1px solid #2c3e50;
  border-bottom: 1px solid #2c3e50;
  background: #ccc;
  width: 100%;
  padding-left: 20px;
  height: 45px;
}
</style>
