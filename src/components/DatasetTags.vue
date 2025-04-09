<template>
  <div class="q-mt-md">
    <div class="row items-center">
      <div class="dataset-tags">
        <q-chip
          v-for="tag in datasetTags"
          :key="tag"
          removable
          outline
          square
          color="grey-7"
          @remove="removeDatasetTag(tag)"
        >
          {{ tag }}
        </q-chip>
      </div>
      <q-space />
      <div class="q-mx-md">
        <q-input
          v-if="showNewTagInput"
          v-model="newTagName"
          bottom-slots
          hint="Enter a dataset tag and press Enter"
          dense
          autofocus
          @keyup.enter="saveNewTag"
        >
          <template #append>
            <q-btn
              round
              dense
              flat
              color="green-4"
              icon="fa fa-plus"
              @click="saveNewTag"
            />
            <q-btn
              round
              color="red-4"
              dense
              flat
              icon="fa fa-times"
              @click="showNewTagInput = false"
            />
          </template>
        </q-input>
        <q-btn
          v-else
          outline
          label="Add Dataset Tag"
          :color="$q.dark.isActive ? 'grey-3' : 'grey-9'"
          @click="showNewTagInput = true"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { mapState, mapMutations } from "vuex";

export default {
  name: "DatasetTags",
  data() {
    return {
      showNewTagInput: false,
      newTagName: "",
    };
  },
  computed: {
    ...mapState(["datasetTags"]),
  },
  methods: {
    ...mapMutations(["addDatasetTag", "removeDatasetTag"]),
    saveNewTag() {
      if (!this.newTagName) return;
      this.addDatasetTag(this.newTagName);
      this.newTagName = "";
      this.showNewTagInput = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.dataset-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  padding: 0.5rem;
}
</style>