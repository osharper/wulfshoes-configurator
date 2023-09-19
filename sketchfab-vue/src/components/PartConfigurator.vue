<script>
import { computed } from 'vue';
import { useSketchfabStore } from '../stores/sketchfab';

import ColorSelector from './ColorSelector.vue';
import MaterialSelector from './MaterialSelector.vue';
import CheckboxSwitch from './CheckboxSwitch.vue';

export default {
  components: {
    ColorSelector,
    MaterialSelector,
    CheckboxSwitch,
},
  props: {
    part: {
      type: Object,
      default: () => ({}),
    },
  },
  setup() {
    const sketchfabStore = useSketchfabStore();

    const availableMaterials = computed(() => sketchfabStore.availableMaterials);
    const availableColors = computed(() => sketchfabStore.availableColors);
    const customizationDescription = computed(() => sketchfabStore.getCustomizationDescription());

    return {
        availableMaterials,
        availableColors,
        customizationDescription,
    };
  },
  methods: {
    selectColor(index) {
      useSketchfabStore().selectColor(index);
    },

    selectMaterial(index) {
      useSketchfabStore().selectMaterial(index);
    },
    removePart(doRemove) {
      if (doRemove) useSketchfabStore().removePart(this.part);
      else useSketchfabStore().restorePart(this.part);
    },
  },
};
</script>


<template>
    <div class="sidebar">
        <div v-if="part && part.removable" class="sidebar-top">
            <label>Remove:</label><CheckboxSwitch :value="part.removed" @value-updated="removePart" />
        </div>
        <div class="part-config" v-if="part && !part.removed">
            <MaterialSelector :materials="availableMaterials" @material-selected="selectMaterial" />
            <ColorSelector :colors="availableColors" @color-selected="selectColor" />
        </div>
        <div v-if="customizationDescription" class="sidebar-bottom">
            <h3>Customization desription:</h3>
            <p>{{ customizationDescription }}</p>
        </div>
    </div>
</template>


<style scoped>
.sidebar {
    overflow: auto;
    flex: 0 0 20%;
    overflow: auto;
    display: flex;
    flex-direction: column;
}
.sidebar-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1em;
    border-bottom: 1px solid #dddddd;
}
.sidebar .part-config {
    display: flex;
    flex-direction: row;
}

.sidebar-bottom {
    padding: 1em;
    border-top: 1px solid #dddddd;
    margin-top: auto;
}

@media (min-width: 1024px) {
  .sidebar {
    padding-left: 2em;
  }
}
</style>
