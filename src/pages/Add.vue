<template>
  <div class="q-pa-md" style="max-width: 600px">
    <div class="text-h4 q-mb-lg">Add New Smoothie Recipe</div>
    <q-form @submit.prevent="addSmoothie" class="q-gutter-md">
      <q-input
        filled
        v-model="title"
        label="Smoothie title"
        lazy-rules
        :rules="[(val) => (val && val.length > 0) || 'Please type something']"
      />

      <!-- <div v-for="(ing, index) in ingredients" :key="index">
        <q-input filled label="ingredient:" v-model="ingredients[index]" />
      </div> -->

      <q-input
        filled
        label="Add an ingredient:"
        @keydown.tab.prevent="addIng"
        v-model="another"
        lazy-rules
      />

      <q-btn color="grey" rounded class="q-ml-md" v-for="(ing, index) in ingredients" :key="index">
        {{ ing }}
        <!-- <q-badge color="red" rounded floating @click="deleteIng(ing)">x</q-badge> -->
        <q-badge color="red" rounded floating @click="deleteIng(index)">x</q-badge>
      </q-btn>

      <div class="q-gutter-sm">
        <q-btn label="Submit" type="submit" color="primary" class="" />
        <q-btn
          label="Reset"
          color="secondary"
          class=""
          @click="resetSmoothie"
        />
      </div>
    </q-form>
  </div>
</template>

<script>
import { ref } from "vue";
import { fireDB } from "src/boot/firebase";
import slugify from "slugify";
import { useRouter } from "vue-router";

export default {
  setup() {
    const title = ref(null);
    const another = ref(null);
    const ingredients = ref([]);
    const slug = ref(null);

    const router = useRouter();

    const addIng = () => {
      if (another.value) {
        ingredients.value.push(another.value);
        console.log("ingredient: ", ingredients.value);
        another.value = null;
      }
    };

    // use filter 
    // const deleteIng = (ing) => {
    //   ingredients.value = ingredients.value.filter(ingredient => ingredient != ing)
    // }

    // use index
    const deleteIng = (index) => {
      ingredients.value.splice(index, 1)
    }

    const resetSmoothie = () => {
      ingredients.value = null;
      title.value = null;
    };

    const addSmoothie = () => {
      // create a slug
      slug.value = slugify(title.value, {
        replacement: "-",
        remove: /[$*_+~.()'"!\-:@]/g,
        lower: true,
      });

      fireDB
        .collection("ninja-smoothies")
        .add({
          id: Date.now(),
          ingredients: ingredients.value,
          slug: slug.value,
          title: title.value,
        })
        .then(() => {
          router.push("/");
        });
    };

    return {
      title,
      another,
      ingredients,
      addIng,
      resetSmoothie,
      addSmoothie,
      deleteIng
    };
  },
};
</script>

<style lang="scss" scoped></style>
