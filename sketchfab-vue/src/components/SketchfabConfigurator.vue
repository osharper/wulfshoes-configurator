<script>
import { computed, onMounted, ref } from 'vue';
import { useSketchfabStore } from '../stores/sketchfab';
import PartSelector from './PartSelector.vue';
import PartConfigurator from './PartConfigurator.vue';

export default {
  components: {
    PartSelector,
    PartConfigurator
  },
  props: {
    modelid: {
      type: String,
      required: true,
    },
    config: {
      type: Object,
      default: () => ({}),
    },
  },
  setup({ modelid, config }) {
    const sketchfabStore = useSketchfabStore();

    sketchfabStore.config.modelId = modelid;
    sketchfabStore.parts = config.parts;

    // declare a ref to hold the element reference
    // the name must match template ref value
    const sketchfabIframe = ref(null);

    const parts = computed(() => sketchfabStore.parts);
    const activePart = computed(() => sketchfabStore.activePart);
    const activePartIndex = computed(() => sketchfabStore.parts.indexOf(sketchfabStore.activePart));

    const isSketchfabReady = computed(() => sketchfabStore.isSketchfabReady);

    onMounted(() => {
      sketchfabStore.initializeSketchfab(sketchfabIframe.value, config)
        .then(() => sketchfabStore.selectPart(0));
    });

    return {
      isSketchfabReady,
      iframeWrapperClass: computed(() => ({
        'sketchfab-ready': isSketchfabReady.value,
      })),
      sketchfabIframe,
      parts,
      activePart,
      activePartIndex,
    };
  },
  methods: {
    selectPart(index) {
      useSketchfabStore().selectPart(index);
    },
  },
};
</script>


<template>
  <div class="top-selector">
    <PartSelector :parts="parts" :selected-index="activePartIndex" @part-selected="selectPart" />
  </div>

  <div class="main-row">
    <div :class="iframeWrapperClass" class="sketchfab-vue-wrapper">
      <!-- Use the ref attribute to give the element a name -->
      <iframe ref="sketchfabIframe"></iframe>
    </div>

    <PartConfigurator :part="activePart" />
  </div>
</template>


<style scoped>
.main-row {
  flex: 1 0 auto;
  display: flex;
}

.sketchfab-vue-wrapper {
    position: relative;
    flex: 1 1 auto;
    background: #ccc;
}

.sketchfab-vue-wrapper iframe {
    display: block;
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    border: 0;
}

.sidebar {
    overflow: auto;
    position: relative;
    flex: 0 0 20%;
    overflow: auto;
    padding-left: 2em;
}

@media (min-width: 1024px) {
  .sidebar {
    padding-left: 2em;
  }
}
</style>
