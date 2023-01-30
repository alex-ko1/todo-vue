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
    <div v-if="unComplTasks.length > 0" class="uncompleted-tasks">
      <div
        class="task-wrapper"
        v-for="(Ttask, index) in unComplTasks"
        :key="Ttask.id"
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

    <div class="completed-tasks">
      <h1 class="title">Done</h1>
      <div
        class="task-wrapper"
        v-for="(Ttask, index) in completeTasks"
        :key="Ttask.id"
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
export default {
  data() {
    return {
      limit: 5,
    };
  },
  components: { TaskItem },
  props: {
    unComplTasks: {
      type: Array,
      required: true,
    },
    completeTasks: {
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
        const completeMask = this.unComplTasks.splice(index, 1);
        this.completeTasks.unshift(...completeMask);
        completeMask[0].status = "complete";
      } else {
        const noCompleteMask = this.completeTasks.splice(index, 1);
        this.unComplTasks.push(...noCompleteMask);
        noCompleteMask[0].status = "need";
      }
    },
    // Removing a task from the to-do list.
    removeTask(index, type) {
      const toDoList = type === "need" ? this.unComplTasks : this.completeTasks;
      toDoList.splice(index, 1);
    },
    showMore() {
      this.limit += 5;
    },
  },
};
</script>

<style scoped lang="scss">
.all-tasks {
  width: 80%;
  margin: 0 auto;
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
</style>
