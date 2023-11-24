<script >
import CustomButton from "./CustomButton.vue";

export default {
  props: {
    list: Array,
    listKey: String,
    type: String,
    title: String,
  },
  components: {
    CustomButton,
  },
  data() {
    return {
      inputValue: "",
      isCreating: false,
    };
  },
  methods: {
    handleCreate() {
      this.isCreating = true;
    },
    handleChange(event) {
      this.inputValue = event.target.innerText;
    },
    handleSubmit() {
      this.$emit("add-card", this.inputValue);
      this.isCreating = false;
    },
    handleDrop() {
      console.log(event);
    },
  },
  watch: {
    isCreating() {
      if (this.isCreating) {
        setTimeout(() => {
          this.$refs["input-area"].focus();
        });
      }
    },
  },
  mounted() {
    this.$emit('mount-element', this.$refs[this.type])
  }
};
</script>

<template>
  <div class="container" :ref="type">
    <p :class="`title-2 ${type}`">{{ title }}</p>
    <div class="list" @drop="handleDrop">
      <div v-if="list.length" class="cards">
        <div
          v-for="(card, index) in list"
          :key="index"
          class="card"
          @mousedown="$emit('hold', { listKey: listKey, index: index })"
        >
          {{ card }}
        </div>
      </div>
      <div class="input-card-container" v-if="isCreating">
        <div class="input-card card">
          <span
            name=""
            id=""
            class="text-area-card"
            ref="input-area"
            role="textarea"
            contenteditable
            @input="(event) => handleChange(event)"
          ></span>
        </div>
        <div class="option-buttons">
          <button @click="isCreating = false">Cancel</button>
          <button @click="handleSubmit">Save</button>
        </div>
      </div>
      <custom-button
        v-if="type === 'to-do' && !isCreating"
        :content="'Create new card'"
        :eventName="'create-card'"
        @create-card="handleCreate"
      />
    </div>
  </div>
</template>


<style lang="scss" scoped>
@import "./../style/container.scss";
</style>