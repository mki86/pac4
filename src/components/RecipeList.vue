<template>
  <div id="recipe-list" class="recipe-list">
    <div v-for="recipe in recipeList" :key="recipe.id">
      <recipeCard :recipe="recipe" v-on:delete-recipe="deleteRecipe" />
    </div>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import RecipeCard from "./RecipeCard.vue";

export default defineComponent({
  name: "RecipeList",
  props: {
    recipeList: {
      type: Object,
      required: true,
    },
  },
  components: { RecipeCard },
  setup(props, context) {
    const listaRecetas = ref(props.recipeList);

    function deleteRecipe(recipeId) {
      context.emit("delete-recipe", recipeId);
    }

    return { deleteRecipe, listaRecetas };
  }, 
});
</script>

<style scoped>
.recipe-list {
  display: grid;
  width: 100%;
  grid-template-columns: 1fr 1fr 1fr;
}
</style>
