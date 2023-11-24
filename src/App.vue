
<script>
import Container from "./components/Container.vue";
import { LIST_CONTAINER } from "./constants/listContainer.js";

export default {
  components: {
    Container,
  },
  data() {
    return {
      LIST_CONTAINER: LIST_CONTAINER,
      cloneItem: {
        value: "",
        listKey: "",
        index: 0,
      },
      lists: {
        listToDo: [],
        listInProgress: [],
        listDone: [],
      },
      draggingStatus: "",
      containerRefs: [],
    };
  },
  methods: {
    handleAddCard(key, payload) {
      this.lists[key] = [...this.lists[key], payload];
    },
    handleHold(payload) {
      event.preventDefault();
      this.draggingStatus = "dragging";
      const cardCloneDOM = this.$refs["card-clone"];
      this.cloneItem = {
        value: event.target.innerText,
        listKey: payload.listKey,
        index: payload.index,
      };
      cardCloneDOM.style.transform = `translate(${-event.offsetX}px, ${-event.offsetY}px)`;
      cardCloneDOM.style.left = `${event.clientX}px`;
      cardCloneDOM.style.top = `${event.clientY}px`;
      this.removeItem(payload.listKey, payload.index);
    },
    handleMove() {
      if (this.draggingStatus === "dragging") {
        const cardCloneDOM = this.$refs["card-clone"];
        cardCloneDOM.style.left = `${event.clientX}px`;
        cardCloneDOM.style.top = `${event.clientY}px`;
      }
    },
    handleDrop() {
      if (this.draggingStatus === "dragging") {
        this.draggingStatus = "";
        this.configAppendCard(event.clientX, event.clientY);
      }
    },
    removeItem(key, index) {
      this.lists[key].splice(index, 1);
    },
    configAppendCard(left, top) {
      let listKeyFound = "";
      LIST_CONTAINER.forEach((list, index) => {
        const rect = this.containerRefs[index].getBoundingClientRect();
        if (
          left >= rect.left &&
          top >= rect.top &&
          left <= rect.right &&
          top <= rect.bottom
        ) {
          listKeyFound = list.list_key;
        }
      });
      if (listKeyFound) {
        this.appendCard(listKeyFound);
      } else {
        this.appendCard(this.cloneItem.listKey);
      }
    },
    appendCard(key) {
      this.lists[key] = [...this.lists[key], this.cloneItem.value];
    },
  },
};
</script>

<template>
  <div class="main-view" @mousemove="handleMove" @mouseup="handleDrop">
    <p class="title">Jira fake</p>
    <div class="trello-area">
      <container
        v-for="item in LIST_CONTAINER"
        :key="item.id"
        :list="lists[item.list_key]"
        :listKey="item.list_key"
        :type="item.type"
        :title="item.title"
        @add-card="(payload) => handleAddCard(item.list_key, payload)"
        @hold="(payload) => handleHold(payload)"
        @mount-element="(element) => containerRefs.push(element)"
      />
    </div>
    <div :class="`card card-clone ${draggingStatus}`" ref="card-clone">
      {{ cloneItem.value }}
    </div>
  </div>
</template>

<style lang="scss">
@import "./style/app.scss";
</style>