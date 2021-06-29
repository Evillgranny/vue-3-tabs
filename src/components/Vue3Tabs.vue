<template>
  <div>
    <div class="relative" :class="[...buttonsWrapperClass]">
      <div
        v-for="(button, idx) in buttons"
        :key="'btn' + idx"
        :ref="buttonInDOM"
        :class="[...buttonWrapperClass]"
        @click="activeIdx = idx"
      >
        <slot :activeIdx="activeIdx" :button="button" :idx="idx" name="button">
          <button class="text-xl">{{ button.label }}</button>
        </slot>
      </div>
      <div
        v-if="trace"
        :class="traceTopClass"
        class="absolute h-3px w-full rounded-full bg-gray"
      ></div>
      <div
        class="absolute h-3px transition-all duration-500 rounded-full z-10"
        :class="[traceTopClass, ...buttonBorderClass]"
        :style="{ width: lineWidth, left: linePosition }"
      ></div>
    </div>

    <div class="pt-12">
      <div
        v-for="(v, idx) in buttons"
        :key="idx"
        :class="{ hidden: idx !== activeIdx }"
      >
        <slot :name="v.slotName">
          {{ "Default:" + idx }}
        </slot>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, defineComponent, computed, PropType, watch } from "vue";
export default defineComponent({
  props: {
    buttons: {
      type: Array,
      required: true,
    },
    trace: {
      type: Boolean,
      required: false,
      default: false,
    },
    traceTopClass: {
      type: String,
      required: false,
      default: "top-full",
    },
    buttonBorderClass: {
      type: [Array, String],
      required: false,
      default: () => ["bg-text"],
    },
    buttonsWrapperClass: {
      type: [Array, String],
      required: false,
      default: () => ["flex", "justify-between"],
    },
    buttonWrapperClass: {
      type: [Array, String],
      required: false,
      default: () => [""],
    },
  },
  setup(props) {
    let activeIdx = ref<number>(0);
    let lineWidth = ref<number | string>(0);
    let linePosition = ref<number | string>(0);
    let itemRefs: Array<any> = [];

    const buttonInDOM = (el: any): void => {
      if (el) {
        itemRefs.push(el);
      }
    };

    const changeLine = () => {
      if (props.buttons.length) {
        lineWidth.value = itemRefs[activeIdx.value].offsetWidth + "px";
        linePosition.value = itemRefs[activeIdx.value].offsetLeft + "px";
      }
    };

    return { activeIdx, lineWidth, linePosition, buttonInDOM, changeLine };
  },
});
</script>

<style>
.hidden {
  display: none;
}
</style>
