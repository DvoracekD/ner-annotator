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
          clickable
          color="grey-7"
          @remove="removeDatasetTag(tag)"
          @click.stop="showAssignedTags(tag)"
        >
          {{ tag }}
          <q-tooltip>Click to see assigned document tags</q-tooltip>
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

    <q-dialog v-model="showDialog" persistent>
      <q-card style="min-width: 350px">
        <q-card-section class="row items-center">
          <div class="text-h6">Document Tags for "{{ selectedDatasetTag }}"</div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>

        <q-card-section class="q-pt-none">
          <div v-if="!assignedDocumentTags.length">
            No document tags are assigned to this dataset tag.
          </div>
          <div v-else>
            <div v-for="(tags, docIndex) in groupedByDocument" :key="docIndex" class="q-my-md">
              <div class="text-subtitle2">Document {{parseInt(docIndex.replace('doc', '')) + 1}}:</div>
              <q-chip
                v-for="tag in tags"
                :key="tag.tokenId"
                outline
                square
                color="grey-7"
              >
                {{ tag.text }}
                <q-tooltip>Label: {{ tag.label }}</q-tooltip>
              </q-chip>
            </div>
          </div>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Close" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
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
      showDialog: false,
      selectedDatasetTag: null
    };
  },
  computed: {
    ...mapState(["datasetTags", "tagAssignments"]),
    assignedDocumentTags() {
      if (!this.selectedDatasetTag) return [];
      
      // Get all document tags assigned to this dataset tag
      return this.tagAssignments[this.selectedDatasetTag] || [];
    },
    groupedByDocument() {
      // Group document tags by document index for better organization
      const grouped = {};
      this.assignedDocumentTags.forEach(tag => {
        // Extract document index from tokenId (format is 'doc{index}:{start}:{label}')
        const docIdMatch = tag.tokenId.match(/^doc(\d+):/);
        if (docIdMatch) {
          const docId = `doc${docIdMatch[1]}`;
          if (!grouped[docId]) {
            grouped[docId] = [];
          }
          grouped[docId].push(tag);
        }
      });
      return grouped;
    }
  },
  methods: {
    ...mapMutations(["addDatasetTag", "removeDatasetTag"]),
    saveNewTag() {
      if (!this.newTagName) return;
      this.addDatasetTag(this.newTagName);
      this.newTagName = "";
      this.showNewTagInput = false;
    },
    showAssignedTags(tag) {
      this.selectedDatasetTag = tag;
      this.showDialog = true;
    }
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