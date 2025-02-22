<template>
  <span class="dimensions">
    <Validate :state="formstate" tag="span">
      <PropertyInputNumber
        ref="xInput"
        v-model="x"
        :name="`${name}-x`"
        :schema-property="schemaProperty.items"
        :required="required || dimensionsSpecified"
        :hint="hints[0]"
        @focus.native="onFocus()"
        @blur.native="onBlur($event)" />
    </Validate>
    &times;
    <Validate :state="formstate" tag="span">
      <PropertyInputNumber
        v-model="y"
        :name="`${name}-y`"
        :schema-property="schemaProperty.items"
        :required="required || dimensionsSpecified"
        :hint="hints[1]"
        @focus.native="onFocus()"
        @blur.native="onBlur($event)" />
    </Validate>
    &times;
    <Validate :state="formstate" tag="span">
      <PropertyInputNumber
        v-model="z"
        :name="`${name}-z`"
        :schema-property="schemaProperty.items"
        :required="required || dimensionsSpecified"
        :hint="hints[2]"
        @focus.native="onFocus()"
        @blur.native="onBlur($event)" />
    </Validate>
    {{ unit }}
  </span>
</template>

<script>
import { arrayProp, booleanProp, objectProp, stringProp } from 'vue-ts-types';
import PropertyInputNumber from './PropertyInputNumber.vue';

export default {
  components: {
    PropertyInputNumber,
  },
  model: {
    prop: `dimensions`,
  },
  props: {
    dimensions: arrayProp().withDefault(null),
    hints: arrayProp().withDefault(() => [`x`, `y`, `z`]),
    schemaProperty: objectProp().required,
    unit: stringProp().optional,
    required: booleanProp().withDefault(false),
    name: stringProp().required,
    formstate: objectProp().required,
  },
  data() {
    return {
      validationData: {
        'complete-dimensions': ``,
      },
    };
  },
  computed: {
    x: {
      get() {
        return this.dimensions ? this.dimensions[0] : null;
      },
      set(xInput) {
        this.$emit(`input`, getDimensionsArray(xInput, this.y, this.z));
      },
    },
    y: {
      get() {
        return this.dimensions ? this.dimensions[1] : null;
      },
      set(yInput) {
        this.$emit(`input`, getDimensionsArray(this.x, yInput, this.z));
      },
    },
    z: {
      get() {
        return this.dimensions ? this.dimensions[2] : null;
      },
      set(zInput) {
        this.$emit(`input`, getDimensionsArray(this.x, this.y, zInput));
      },
    },
    dimensionsSpecified() {
      return this.dimensions !== null;
    },
  },
  mounted() {
    this.$emit(`vf:validate`, this.validationData);
  },
  methods: {
    onFocus() {
      this.$emit(`focus`);
    },
    onBlur(event) {
      if (!(event.target && event.relatedTarget) || event.target.closest(`.dimensions`) !== event.relatedTarget.closest(`.dimensions`)) {
        this.$emit(`blur`);
      }
    },

    /** @public */
    focus() {
      this.$refs.xInput.focus();
    },
  },
};

/**
 * @param {number | null} x X value of the dimensions array or null.
 * @param {number | null} y Y value of the dimensions array or null.
 * @param {number | null} z Z value of the dimensions array or null.
 * @returns {[number, number, number] | null} Dimensions array with the inputs or null if all inputs were null.
 */
function getDimensionsArray(x, y, z) {
  if (x === null && y === null && z === null) {
    return null;
  }

  return [x, y, z];
}
</script>
