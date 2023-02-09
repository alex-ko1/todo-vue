<template>
  <div
    v-if="unComplTasks.length + completeTasks.length == 0"
    style="text-align: center"
  >
    <div>
      <h2 class="no-tasks">You don't have tasks to do.</h2>
    </div>
  </div>
  <div v-else class="all-tasks">
    <h1 class="title">To do</h1>
    <div
      v-if="unComplTasks.length > 0"
      class="uncompleted-tasks"
      ref="unCompletedTasks"
    >
      <!-- Vue draggable -->
      <!-- <draggable
        :list="unComplTasks"
        class="list-group"
        item-key="body"
        ghost-class="ghost"
        @start="dragging = true"
        @end="dragging = false"
        animation="200"
        ref="unCompletedTasks"
      >
        <template #item="{ element, index }">
          <div class="task-wrapper">
            <task-item
              :task="element"
              @isChecked="isChecked(index, $event)"
              @removeTask="removeTask(index, 'need')"
              ref="taskItem"
              :key="element.id"
            />
          </div>
        </template>
      </draggable> -->

      <!-- Simple drag and drop -->
      <div
        class="task-wrapper"
        v-for="(Ttask, index) in unComplTasks"
        :key="Ttask.id"
        @dragstart="onDragStart($event, Ttask, index)"
        @dragenter="onDragEnter(index)"
        @dragover="onDragOver($event)"
        @dragend="onDragEnd()"
        @dragleave="onDragLeave($event)"
        @drop="onDrop($event)"
        draggable="true"
      >
        <task-item
          :task="Ttask"
          @isChecked="isChecked(index, $event)"
          @removeTask="removeTask(index, 'need')"
        />
      </div>
    </div>

    <div v-else style="text-align: center">
      <div class="congratulations">
        <div>🎊</div>
        <div>All done!</div>
      </div>
    </div>

    <div class="barrier">
      <div class="hr"></div>
      <span class="percent-completed">
        <span class="complete-tasks-count">{{ completeTasks.length }}</span
        >/ <span class="all-tasks-count">{{ allTasksLength }}</span
        >(
        <span class="percentage" :class="percentageColor"
          >{{
            Math.round((completeTasks.length / allTasksLength) * 100)
          }}%</span
        >
        done)
      </span>
    </div>

    <h1 class="title">Done</h1>
    <div class="completed-tasks" ref="completedTasks">
      <div
        class="task-wrapper"
        v-for="(Ttask, index) in completeTasks"
        :key="Ttask.id"
        ref="completedTaskItem"
      >
        <task-item
          :task="Ttask"
          checked
          @isChecked="isChecked(index, $event)"
          @removeTask="removeTask(index, 'complete')"
          v-if="index < limit"
        />
      </div>
      <div
        class="show-more"
        v-if="completeTasks.length > limit"
        @click="showMore"
      >
        Show more...
      </div>
      <div
        class="show-less"
        v-if="limit > 5 && completeTasks.length > 5"
        @click="limit = 5"
      >
        Show less
      </div>
    </div>
  </div>
</template>

<script>
import TaskItem from "./TaskItem.vue";
import draggable from "vuedraggable";

export default {
  data() {
    return {
      limit: 5,

      dragging: false,
      isCheckTask: false,
      isUnCheckTask: false,
      dragIndex: 0,
      dragOverIndex: 0,
    };
  },
  components: { TaskItem, draggable },
  props: {
    unComplTasks: {
      type: Array,
      required: true,
    },
    completeTasks: {
      type: Array,
      required: true,
    },
    allTasks: {
      type: Array,
      required: true,
    },
    addTask: {
      type: Function,
    },
  },
  computed: {
    allTasksLength() {
      return this.unComplTasks.length + this.completeTasks.length;
    },
    // Change color depending on the percentage.
    percentageColor() {
      const percentage = Math.round(
        (this.completeTasks.length / this.allTasksLength) * 100
      );
      if (percentage >= 90) {
        this.isGreen = true;
        return "green";
      } else if (percentage >= 50) {
        this.isYellow = true;
        return "yellow";
      } else if (percentage > 0) return "red";
    },
  },
  methods: {
    // Change the task status from "need" to "complete" or or vice versa.
    isChecked(index, event) {
      if (event.target.checked) {
        event.target.parentElement.classList.add("toDown");
      } else {
        event.target.parentElement.classList.add("toUp");
      }
      event.target.parentElement.style.height =
        event.target.parentElement.clientHeight + "px";
      setTimeout(() => {
        event.target.parentElement.style.height = 0;
        event.target.parentElement.style.marginTop = 0;
      }, 1);
      setTimeout(() => {
        if (event.target.checked) {
          const completeMask = this.unComplTasks.splice(index, 1);
          completeMask[0].status = "complete";
          this.completeTasks.unshift(...completeMask);

          this.$nextTick(() => {
            this.$refs.completedTasks.firstChild.nextSibling.classList.add(
              "upToDown"
            );
            let taskHeight =
              this.$refs.completedTasks.firstChild.nextSibling.clientHeight;
            this.$refs.completedTasks.firstChild.nextSibling.style.height = 0;
            // this.$refs.completedTasks.firstChild.nextSibling.firstChild.style.marginTop = 0;
            setTimeout(() => {
              this.$refs.completedTasks.firstChild.nextSibling.style.height =
                taskHeight + "px";
            }, 10);

            setTimeout(() => {
              this.$refs.completedTasks.firstChild.nextSibling.classList.remove(
                "upToDown"
              );
            }, 500);
          });
        } else {
          const noCompleteMask = this.completeTasks.splice(index, 1);
          this.unComplTasks.push(...noCompleteMask);
          noCompleteMask[0].status = "need";

          // If you use vue draggable library.
          // this.$nextTick(() => {
          //   let taskHeight =
          //     this.$refs.unCompletedTasks.targetDomElement.lastChild
          //       .clientHeight;
          //   this.$refs.unCompletedTasks.targetDomElement.lastChild.style.height = 0;
          //   setTimeout(() => {
          //     this.$refs.unCompletedTasks.targetDomElement.lastChild.style.height =
          //       taskHeight + "px";
          //   }, 10);
          //   this.$refs.unCompletedTasks.targetDomElement.lastChild.classList.add(
          //     "downToUp"
          //   );
          //   setTimeout(() => {
          //     this.$refs.unCompletedTasks.targetDomElement.lastChild.classList.remove(
          //       "downToUp"
          //     );
          //   }, 500);
          // });
        }
      }, 400);
    },

    // Removing a task from the to-do list.
    removeTask(index, type) {
      const toDoList = type === "need" ? this.unComplTasks : this.completeTasks;
      setTimeout(() => {
        toDoList.splice(index, 1);
      }, 300);
    },
    showMore() {
      this.limit += 5;
    },

    // Drag and drop without libraries.
    onDragStart(e, item, index) {
      this.dragIndex = index;
      e.dataTransfer.dropEffect = "move";
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.setData("itemID", item.id);
    },
    onDragEnter(index) {
      this.dragOverIndex = index;
    },
    onDragEnd() {
      let taskItems = [...this.unComplTasks];
      const draggedItemContent = taskItems.splice(this.dragIndex, 1)[0];
      taskItems.splice(this.dragOverIndex, 0, draggedItemContent);
      this.dragIndex = null;
      this.dragOverIndex = null;
      this.$emit("onDragEnd", taskItems);
    },

    onDragOver(e) {
      e.preventDefault();
      if (e.target.classList.contains("task")) {
        e.target.style.boxShadow = "0px 5px 4px -4px rgba(128,128,128,1)";
      }
    },
    onDragLeave(e) {
      e.preventDefault();
      e.target.style.boxShadow = "none";
    },
    onDrop(e) {
      // const ItemId = e.dataTransfer.getData("itemID");
      e.target.style.boxShadow = "none";
    },
  },
};
</script>

<style scoped lang="scss">
.all-tasks {
  width: 80%;
  margin: 0 auto;
}
.list-group {
  transition: 0.3s;
}
.task-wrapper {
  transition: 0.4s;
}
.title {
  text-align: left;
  font-weight: bold;
  font-size: 2.2em;
}
.barrier {
  position: relative;
  display: flex;
  justify-content: space-around;
  margin: 20px 0 0;
}
.hr {
  width: 80%;
  border-bottom: 1px solid #ccc;
  height: 13px;
}
.percent-completed {
  font-size: 1rem;
  display: block;
  opacity: 0.6;
}
.congratulations {
  font-size: 1.6em;
}
.show-more,
.show-less {
  cursor: pointer;
  &:hover {
    text-decoration: underline;
  }
}
.show-less {
  text-align: right;
}
.red {
  color: red;
}
.yellow {
  color: orange;
}
.green {
  color: green;
}
.ghost {
  background-color: rgb(30, 144, 255, 0.1);
  padding: 0 10px;
  border-radius: 10px;
  z-index: 2;
}
.upToDown {
  animation: toUp 0.5s reverse;
}
.downToUp {
  animation: toDown 0.5s reverse;
}
.toUp {
  animation: toUp 0.5s;
}
.toDown {
  animation: toDown 0.5s;
}
.toUpWrapper {
  animation: toUpWrapper 0.6s;
}
@keyframes toUp {
  25% {
    transform: translateY(0px);
    opacity: 1;
  }
  50% {
  }
  100% {
    transform: translateY(-100px);
    opacity: 0;
  }
}
@keyframes toDown {
  25% {
    transform: translateY(0px);
    opacity: 1;
  }
  50% {
  }
  100% {
    transform: translateY(100px);
    opacity: 0;
  }
}
@media (max-width: 876px) {
  .hr {
    width: 75%;
  }
}
@media (max-width: 768px) {
  .all-tasks {
    width: 90%;
  }
  .percent-completed {
    font-size: 0.8rem;
  }
}
@media (max-width: 576px) {
  .all-tasks {
    width: 100%;
  }
  .hr {
    width: 65%;
  }
}
</style>
