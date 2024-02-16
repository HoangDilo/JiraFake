<script >
import CustomButton from "@/components/CustomButton.vue";
import IcEdit from "@/assets/icons/IcEdit.vue";
import IcTrashCan from "@/assets/icons/IcTrashCan.vue";

export default {
  props: {
    list: Array,
    listKey: String,
    type: String,
    title: String,
  },
  components: {
    CustomButton,
    IcEdit,
    IcTrashCan,
  },
  data() {
    return {
      inputValue: "",
      isCreating: false,
      hoveredCardIndex: undefined,
      editingCardValue: "",
    };
  },
  methods: {
    handleCreate() {
      this.isCreating = true;
      this.inputValue = this.editingCardValue;
    },
    handleEdit(index) {
      this.editingCardValue = this.list[index];
      this.isCreating = true;
      this.$emit("delete-card", index);
    },
    handleDelete(index) {
      this.$emit("delete-card", index);
    },
    handleChange() {
      this.inputValue = event.target.innerText;
    },
    handleSubmit() {
      if (this.inputValue) {
        this.$emit("add-card", this.inputValue);
        this.editingCardValue = "";
      } else if (this.editingCardValue) {
        this.$emit("add-card", this.editingCardValue);
      }
      this.isCreating = false;
    },
    handleCancel() {
      if (this.editingCardValue) {
        this.$emit("add-card", this.editingCardValue);
        this.editingCardValue = "";
      }
      this.isCreating = false;
    },
    handleUsingOption() {
      event.preventDefault();
      event.stopPropagation();
    },
  },
  watch: {
    isCreating() {
      if (this.isCreating) {
        setTimeout(() => {
          const range = document.createRange();
          range.selectNodeContents(this.$refs["input-area"]);
          range.collapse(false);
          window.getSelection().removeAllRanges();
          window.getSelection().addRange(range);
        });
      }
    },
  },
  mounted() {
    this.$emit("mount-element", this.$refs[this.type]);
  },
};
</script>

<template>
  <div class="container" :ref="type">
    <p :class="`title-2 ${type}`">{{ title }}</p>
    <div class="list">
      <div v-if="list.length" class="cards">
        <div
          class="card-container"
          v-for="(card, index) in list"
          :key="index"
          @mousedown="$emit('hold', index)"
          @mouseenter="hoveredCardIndex = index"
          @mouseleave="hoveredCardIndex = undefined"
        >
          <div class="card">
            <span>
              {{ card }}
            </span>
          </div>
          <div
            :class="`card-options ${
              hoveredCardIndex === index ? 'hovering' : ''
            }`"
          >
            <div
              class="option"
              @click="handleEdit(index)"
              @mousedown="handleUsingOption"
            >
              <ic-edit />
            </div>
            <div
              class="option"
              @click="handleDelete(index)"
              @mousedown="handleUsingOption"
            >
              <ic-trash-can class="option" />
            </div>
          </div>
        </div>
      </div>
      <div class="input-card-container" v-if="isCreating">
        <div class="input-card card">
          <span
            class="text-area-card"
            ref="input-area"
            role="textarea"
            contenteditable
            @input="handleChange"
            >{{ editingCardValue }}</span
          >
        </div>
        <div class="option-buttons">
          <button @click="handleCancel">Cancel</button>
          <button @click="handleSubmit">Save</button>
        </div>
      </div>
      <custom-button
        v-if="type === 'to-do' && !isCreating"
        :eventName="'create-card'"
        @create-card="handleCreate"
      >
      <p>Create new card</p>
      </custom-button>
    </div>
  </div>
</template>


<style lang="scss" scoped>
@import "@/style/jiraFake.scss";
</style>