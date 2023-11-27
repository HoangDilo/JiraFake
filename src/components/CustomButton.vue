<script>
import IcPlus from "@/assets/icons/IcPlus.vue";
export default {
  components: {
    IcPlus,
  },
  props: {
    eventName: String,
  },
};
</script>

<template>
  <button class="add-new" @click="$emit(eventName)">
    <slot></slot>
    <ic-plus />
  </button>
</template>

<style lang="scss" scoped>
</style>