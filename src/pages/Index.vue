<template>
  <q-page>
    <div class="layout">
      <q-card
        flat
        bordered
        class="my-card bg-grey-1 q-mx-md q-mb-md"
        v-for="item in smoothies"
        :key="item.id"
      >
        <q-card-section>
          <div class="row items-center no-wrap">
            <div class="col">
              <div class="text-h6">{{ item.title }}</div>
            </div>

            <q-fab
              color="teal-8"
              text-color="black"
              icon="keyboard_arrow_down"
              direction="down"
              padding="xs"
              flat
            >
              <q-fab-action
                color="teal-5"
                text-color="white"
                @click="editItem(item.id)"
                icon="edit"
                size="sm"
                padding="xs"
              />
              <q-fab-action
                color="teal-5"
                text-color="white"
                @click="deleteItem(item.id)"
                icon="delete"
                padding="xs"
              />
            </q-fab>
          </div>
        </q-card-section>

        <q-card-actions>
          <q-chip v-for="(ing, index) in item.ingredients" :key="index">{{
            ing
          }}</q-chip>
        </q-card-actions>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import { ref, onMounted } from "vue";
import { fireDB } from "src/boot/firebase";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";

export default {
  setup() {
    const smoothies = ref([]);

    const $q = useQuasar();

    const router = useRouter();

    onMounted(() => {
      fireDB
        .collection("ninja-smoothies")
        .orderBy("id", "desc")
        .onSnapshot((snapshot) => {
          smoothies.value = snapshot.docs.map((doc) => {
            return { ...doc.data(), id: doc.id };
          });
        });
    });

    const editItem = (id) => {
      router.push(`/edit/${id}`);
    };

    const deleteItem = (id) => {
      $q.dialog({
        title: "Warning",
        message: "Are you sure to delete this smoothie permanently ?",
        cancel: true,
        persistent: true,
      })
        .onOk(() => {
          fireDB
            .collection("ninja-smoothies")
            .doc(id)
            .delete()
            .then(() => {
              console.log("item deleted successfully !");
            });
        })
        .onCancel(() => {
          return;
        });
    };

    return {
      smoothies,
      deleteItem,
      editItem,
    };
  },
};
</script>

<style lang="scss" scoped>
.layout {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}
</style>
