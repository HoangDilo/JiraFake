
<script>
import Container from "@/components/Container.vue";
import { LIST_CONTAINER } from "@/constants/listContainer.js";

export default {
  components: {
    Container,
  },
  data() {
    return {
      listContainer: LIST_CONTAINER,
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
      selectedCard: {
        listKey: "",
        index: 0,
      },
    };
  },
  methods: {
    handleAddCard(key, payload) {
      if (payload) {
        this.lists[key] = [...this.lists[key], payload];
      }
    },
    handleHold(listKey, index) {
      event.preventDefault();
      if (this.draggingStatus !== "stop") {
        this.draggingStatus = "holding";
        const cardCloneDOM = this.$refs["card-clone"];
        this.cloneItem = {
          value: event.target.innerText,
          listKey: listKey,
          index: index,
        };
        cardCloneDOM.style.transform = `translate(${-event.offsetX}px, ${-event.offsetY}px)`;
        cardCloneDOM.style.left = `${event.clientX}px`;
        cardCloneDOM.style.top = `${event.clientY}px`;
        this.selectedCard = {
          listKey: listKey,
          index: index,
        };
      }
    },
    handleMove() {
      if (
        this.draggingStatus === "holding" ||
        this.draggingStatus === "dragging"
      ) {
        if (this.draggingStatus === "holding") {
          this.removeItem(this.selectedCard.listKey, this.selectedCard.index);
        }
        this.draggingStatus = "dragging";
        const cardCloneDOM = this.$refs["card-clone"];
        cardCloneDOM.style.left = `${event.clientX}px`;
        cardCloneDOM.style.top = `${event.clientY}px`;
      }
    },
    handleDrop() {
      if (this.draggingStatus === "holding") {
        this.draggingStatus = "";
      } else if (this.draggingStatus === "dragging") {
        this.draggingStatus = "";
        this.configAppendCard(event.clientX, event.clientY);
      }
    },
    removeItem(key, index) {
      this.lists[key].splice(index, 1);
    },
    configAppendCard(left, top) {
      let listKeyFound = "";
      this.listContainer.forEach((list, index) => {
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
        this.lists[listKeyFound] = [
          ...this.lists[listKeyFound],
          this.cloneItem.value,
        ];
      } else {
        this.lists[this.cloneItem.listKey] = [
          ...this.lists[this.cloneItem.listKey],
          this.cloneItem.value,
        ];
      }
    },
  },
};
</script>

<template>
  <div class="main-view" @mousemove="handleMove" @mouseup="handleDrop">
    <p class="title">Jira fake</p>
    <div class="trello-area">
      <container
        v-for="item in listContainer"
        :key="item.id"
        :list="lists[item.list_key]"
        :listKey="item.list_key"
        :type="item.type"
        :title="item.title"
        @add-card="(payload) => handleAddCard(item.list_key, payload)"
        @delete-card="(index) => removeItem(item.list_key, index)"
        @hold="(index) => handleHold(item.list_key, index)"
        @mount-element="(element) => containerRefs.push(element)"
      />
    </div>
    <div :class="`card card-clone ${draggingStatus}`" ref="card-clone">
      {{ cloneItem.value }}
    </div>
  </div>
</template>

<style lang="scss">
@import "@/style/jiraFake.scss";
</style>