<template>
  <g
    :class="dimensionLabels"
    :transform="transformation"
  >
    <text
      v-for="(dimensionName, i) in dimensionNamesNumbered"
      :key="`label-${dimensionName}-${i}`"
      class="dimension-label"
      :transform="`translate(${scale(0.25)}, ${scale(i + 0.5)}) ${axisLabelTransformation}`"
    >
      {{ dimensionName }}
    </text>
    <text
      v-for="i in swaps"
      :key="`label-${i}`"
      class="dimension-swap"
      :transform="`translate(${scale(0.5)}, ${scale(i + 1)}) ${invTransformation}`"
      @click="$emit('swap-dimensions', i)"
    >
      ⇄
    </text>
  </g>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { range } from '@/lib-components/utils';

export default defineComponent({
  name: 'MatrixDimensions',
  props: {
    size: {
      type: Number,
      default: 40,
    },
    location: {
      type: String,
      required: true,
    },
    dimensionNamesNumbered: {
      type: Array as () => string[],
      default: () => [],
    },
    darkMode: {
      type: Boolean,
      default: true,
    },
  },
  emits: ['swap-dimensions'],
  computed: {
    swaps(): number[] {
      return range(this.dimensionNamesNumbered.length - 1);
    },

    /**
     * Global transformation.
     * @todo 'right' and 'bottom'
     */
    transformation(): string {
      switch (this.location) {
        case 'top':
          return '';
        case 'left':
          return 'scale(-1, 1) rotate(90)';
        default:
          return '';
      }
    },

    axisLabelTransformation(): string {
      switch (this.location) {
        case 'top':
          return '';
        case 'left':
          return 'scale(-1, 1)';
        default:
          return '';
      }
    },

    invTransformation(): string {
      switch (this.location) {
        case 'top':
          return '';
        case 'left':
          return 'scale(-1, 1) rotate(90)';
        default:
          return '';
      }
    },
    dimensionLabels(): string {
      return this.darkMode ? 'dimension-labels-dark' : 'dimension-labels-bright';
    },
  },
  methods: {
    scale(i: number): number {
      return i * this.size;
    },
  },
});
</script>

<style scoped lang="scss">
@import "../style-variables.scss";

.dimension-labels-dark, .dimension-labels-bright {
  & .dimension-label {
    font-size: $fontSizeDefault;
    text-anchor: end;
    dominant-baseline: central;
    cursor: default;
    text-transform: uppercase;
    font-weight: 300;
  }
  & .dimension-swap {
    font-size: 18px;
    text-anchor: middle;
    dominant-baseline: central;
    cursor: pointer;
    text-transform: uppercase;
  }
}

.dimension-labels-dark {
  & .dimension-label {
    fill: rgba(255, 255, 255, 0.7);
  }
  & .dimension-swap {
    fill: white;
  }
}

.dimension-labels-bright {
  & .dimension-label {
    fill: rgba(0, 0, 0, 0.9);
  }
  & .dimension-swap {
    fill: rgb(0, 0, 0);
  }
}
</style>
