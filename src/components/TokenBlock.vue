<template>
  <mark :class="'bg-' + backgroundColor">
    <component
      :is="'Token'"
      v-for="t in token.tokens"
      :id="'t' + t.start"
      :key="t.start"
      :token="t"
    />
    <span class="tag" @click="showTagMenu">
      {{ token.label }}
      <small v-if="assignedDatasetTags.length" class="dataset-tags">
        ({{ assignedDatasetTags.join(", ") }})
      </small>
      <q-btn
        icon="fa fa-times-circle"
        round
        flat
        size="xs"
        text-color="grey-7"
        @click.stop="$emit('remove-block', token.start)"
      />
    </span>
    <q-menu v-model="tagMenuOpen" :auto-close="false">
      <q-list style="min-width: 150px">
        <q-item-label header>Dataset Tags</q-item-label>
        <q-item v-if="datasetTags.length === 0">
          <q-item-section>No dataset tags available</q-item-section>
        </q-item>
        <q-item 
          v-for="tag in datasetTags" 
          :key="tag"
          dense
        >
          <q-item-section>
            <q-checkbox
              :model-value="isTagSelected(tag)"
              :label="tag"
              @update:model-value="(val) => handleTagUpdate(tag, val)"
            />
          </q-item-section>
        </q-item>
        <q-separator />
        <q-item clickable v-close-popup>
          <q-item-section>Close</q-item-section>
        </q-item>
      </q-list>
    </q-menu>
  </mark>
</template>

<script>
import Token from "./Token.vue";
import { mapState, mapMutations } from "vuex";

export default {
  name: "TokenBlock",
  components: {
    Token,
  },
  props: {
    token: {
      type: Object,
      required: true,
    },
    backgroundColor: {
      type: String,
      required: false,
    },
  },
  emits: ["remove-block"],
  data() {
    return {
      showClose: false,
      tagMenuOpen: false,
    };
  },
  computed: {
    ...mapState(["datasetTags", "tagAssignments"]),
    assignedDatasetTags() {
      return this.tagAssignments[this.token.label] || [];
    }
  },
  methods: {
    ...mapMutations(["assignDatasetTagToWordTag", "removeDatasetTagFromWordTag"]),
    showTagMenu(event) {
      event.stopPropagation();
      this.tagMenuOpen = true;
    },
    isTagSelected(tag) {
      return this.assignedDatasetTags.includes(tag);
    },
    handleTagUpdate(tag, isChecked) {
      if (isChecked) {
        this.assignDatasetTagToWordTag({ wordTag: this.token.label, datasetTag: tag });
      } else {
        this.removeDatasetTagFromWordTag({ wordTag: this.token.label, datasetTag: tag });
      }
    }
  }
};
</script>

<style lang="scss">
@import "quasar/src/css/variables";

mark {
  padding: 0.5rem;
  position: relative;
  background-color: burlywood;
  box-shadow: 2px 2px 4px rgba(180, 180, 180, 0.4);
  border-radius: 0.25rem;
}
.tag {
  background-color: whitesmoke;
  padding: 4px 0 4px 8px;
  border-radius: 0.25rem;
  font-size: x-small;
  cursor: pointer;
}
.dataset-tags {
  color: #666;
  margin-left: 4px;
}
.close-btn {
  cursor: pointer;
  font-size: small;
  position: absolute;
  width: 1rem;
  height: 1rem;
  padding-left: 0.2rem;
  border-radius: 50%;
  background-color: black;
  color: white;
}
.delete {
  margin-left: 10px;
}
</style>
