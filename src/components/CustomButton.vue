<script>
import IcPlus from "@/assets/icons/IcPlus.vue";
export default {
  components: {
    IcPlus,
  },
  props: {
    eventName: String,
    content: String,
  },
};
</script>

<template>
  <button class="add-new" @click="$emit(eventName)">
    <p>{{ content }}</p>
    <ic-plus />
  </button>
</template>

<style lang="scss" scoped>
</style>