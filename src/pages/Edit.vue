<template>
  <div class="q-pa-md" style="max-width: 600px">
    <div class="text-h4 q-mb-lg">Edit Smoothie Recipe</div>
    <q-form @submit.prevent="updateSmoothie" class="q-gutter-md">
      <q-input
        filled
        v-model="title"
        label="Smoothie title"
        lazy-rules
        :rules="[(val) => (val && val.length > 0) || 'Please type something']"
      />

      <q-input
        filled
        label="Add an ingredient:"
        @keydown.tab.prevent="addIng"
        v-model="another"
        lazy-rules
      >
        <template v-slot:append>
          <q-avatar>
            <q-icon name="add_box" size="sm" @click="addIng" />
          </q-avatar>
        </template>
      </q-input>

      <q-btn
        color="grey"
        rounded
        class="q-ml-md"
        v-for="(ing, index) in ingredients"
        :key="index"
      >
        {{ ing }}
        <!-- <q-badge color="red" rounded floating @click="deleteIng(ing)">x</q-badge> -->
        <q-badge color="red" rounded floating @click="deleteIng(index)"
          >x</q-badge
        >
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
import { ref, onMounted } from "vue";
import { fireDB } from "src/boot/firebase";
import slugify from "slugify";
import { useRouter, useRoute } from "vue-router";

export default {
  setup() {
    const title = ref(null);
    const another = ref(null);
    const ingredients = ref([]);
    const slug = ref(null);

    const smoothies = ref([]);

    const route = useRoute();
    const router = useRouter();

    onMounted(() => {
      fireDB
        .collection("ninja-smoothies")
        .doc(route.params.id)
        .get()
        .then((doc) => {
          title.value = doc.data().title
          ingredients.value = doc.data().ingredients
        });
    });

    const addIng = () => {
      if (another.value) {
        ingredients.value.push(another.value);
        console.log("ingredients: ", ingredients.value);
        another.value = null;
      }
    };

    // use filter
    // const deleteIng = (ing) => {
    //   ingredients.value = ingredients.value.filter(ingredient => ingredient != ing)
    // }

    // use index
    const deleteIng = (index) => {
      ingredients.value.splice(index, 1);
      console.log("ingredients: ", ingredients.value);
    };

    const resetSmoothie = () => {
      ingredients.value = null;
      title.value = null;
    };

    const updateSmoothie = () => {
      // create a slug
      slug.value = slugify(title.value, {
        replacement: "-",
        remove: /[$*_+~.()'"!\-:@]/g,
        lower: true,
      });

      fireDB
        .collection("ninja-smoothies")
        .doc(route.params.id)
        .update({     
          title: title.value,
          ingredients: ingredients.value,
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
      updateSmoothie,
      deleteIng,
      smoothies,
    };
  },
};
</script>

<style lang="scss" scoped></style>
