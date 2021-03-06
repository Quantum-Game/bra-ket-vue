<template>
  <div class="ket-viewer">
    <ket
      class="ket"
      :vector="vector"
      :selected-option="selectedOption"
      :all-bases="activeBases"
      :dark-mode="darkMode"
    />
    <hr
      v-if="showLegend"
      :class="darkMode ? 'hr-dark' : 'hr-bright'"
    >
    <coordinate-legend
      v-if="showLegend"
      :complex-style="selectedOption"
      :dimension-names="dimensionNames"
      :dark-mode="darkMode"
    />
    <hr :class="darkMode ? 'hr-dark' : 'hr-bright'">
    <options-group
      class="options"
      :options="options"
      :dark-mode="darkMode"
      :selected-option="selectedOption"
      @selected="selectedOption = $event"
    />

    <span>
      <span
        v-for="bases in activeBases"
        :key="`basis-${bases.name}`"
      >
        <span
          v-if="bases.name == 'polarization'"
        >
          <options-group-svg
            v-if="dimensionNames.indexOf(bases.name) > -1"
            :key="`options-group-basis-${bases.name}`"
            :dark-mode="darkMode"
            :options="bases.availableBases"
            :selected-option="bases.selected"
            @selected="bases.selected = $event"
          />
        </span>
        <span
          v-else
        >
          <options-group
            v-if="dimensionNames.indexOf(bases.name) > -1"
            :key="`options-group-basis-${bases.name}`"
            :dark-mode="darkMode"
            :options="bases.availableBases"
            :selected-option="bases.selected"
            @selected="bases.selected = $event"
          />
        </span>
      </span>
    </span>
  </div>
</template>

<script lang="ts">
import {
  computed,
  defineComponent,
  PropType,
  proxyRefs,
  ref,
  watch,
} from 'vue';
import { Vector } from 'quantum-tensors';
import CoordinateLegend from '@/lib-components/coordinate-legend.vue';
import OptionsGroup from '@/lib-components/options-group.vue';
import OptionsGroupSvg from '@/lib-components/options-group-svg.vue';
import Ket from '@/lib-components/ket.vue';

export default defineComponent({
  name: 'KetViewer',
  components: {
    CoordinateLegend,
    OptionsGroup,
    OptionsGroupSvg,
    Ket,
  },
  props: {
    vector: { type: Object as PropType<Vector>, required: true },
    showLegend: { type: Boolean, default: true },
    darkMode: { type: Boolean, default: true },
    polBasis: { type: String, default: 'HV' },
  },
  setup(props) {
    const innerPolBasis = ref(props.polBasis);
    watch(() => props.polBasis, (basis) => {
      innerPolBasis.value = basis;
    });

    const allBases = [
      proxyRefs({ name: 'polarization', availableBases: ['HV', 'DA', 'LR'], selected: innerPolBasis }),
      proxyRefs({ name: 'spin', availableBases: ['spin-x', 'spin-y', 'spin-z'], selected: ref('spin-z') }),
      proxyRefs({ name: 'qubit', availableBases: ['01', '+-', '+i-i'], selected: ref('01') }),
    ];

    const activeBases = computed(() => {
      const { names } = props.vector;
      return allBases.filter((base) => names.includes(base.name));
    });

    return {
      dimensionNames: computed(() => props.vector.names),
      options: ['polar', 'polarTau', 'cartesian', 'color'],
      selectedOption: ref('polar'),
      activeBases,
    };
  },
});
</script>

<style lang="scss" scoped>
@import "../style-variables.scss";

.ket-viewer {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  transition: height 0.5s;
  overflow: hidden;
  font-family: $mainFont;
  @media screen and (max-width: 1000px) {
    border: none;
  }
}

.ket {
  padding: 5px 0px;
}

.options {
  margin-top: 5px;
}

.hr-dark, .hr-bright {
  border: 0;
  height: 0;
  margin: 0;
  width:100%
}

.hr-dark {
  border-bottom: 1px solid rgba(255, 255, 255, 0.3);
}

.hr-bright {
  border-bottom: 1px solid rgba(0, 0, 0, 0.6);
}
</style>
