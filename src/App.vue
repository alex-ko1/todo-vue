<template>
  <div class="wrapper">
    <my-input
      v-bind:value="valueInput"
      @input="updateInput"
      @keypress.enter="addTask"
    />
    <input type="button" value="Add" class="btn btn-add" @click="addTask" />
    <task-list
      :unComplTasks="unCompleteTasksList"
      :completeTasks="completeTasksList"
    />
  </div>

  <!-- Delete code -->

  <!-- <div class="all-tasks">
    <h1 class="title">To do</h1>
    <div
      class="task"
      v-for="(Ttask, index) in unCompleteTasksList"
      :key="Ttask.id"
    >
      <task-item
        :task="Ttask"
        @isChecked="isChecked(index, $event)"
        @removeTask="removeTask(index, 'need')"
      />
    </div>

    <div class="barrier">
      <div class="hr"></div>
      <span class="percent-completed">
        <span class="complete-tasks-count">{{ completeTasksList.length }}</span
        >/
        <span class="all-tasks-count">{{
          unCompleteTasksList.length + completeTasksList.length
        }}</span
        >(
        <span class="percentage"
          >{{
            unCompleteTasksList.length + completeTasksList.length == 0
              ? 100
              : Math.round(
                  (completeTasksList.length /
                    (unCompleteTasksList.length + completeTasksList.length)) *
                    100
                )
          }}%</span
        >
        done)
      </span>
    </div>

    <div class="completed-tasks">
      <h1 class="title">Done</h1>
      <div
        class="task"
        v-for="(Ttask, index) in completeTasksList"
        :key="Ttask.id"
      >
        <task-item
          :task="Ttask"
          @isChecked="isChecked(index, $event)"
          @removeTask="removeTask(index, 'complete')"
          checked
        />
      </div>
    </div>
  </div> -->
</template>

<script>
import MyInput from "./components/MyInput.vue";
import TaskList from "./components/TaskList.vue";
import TaskItem from "./components/TaskItem.vue";
import { Base64 } from "https://cdn.jsdelivr.net/npm/js-base64@3.7.4/base64.mjs";
import { warn } from "@vue/runtime-core";

export default {
  components: { MyInput, TaskList, TaskItem },
  data() {
    return {
      unCompleteTasksList: [],
      completeTasksList: [],
      allTasksList: [],
      valueInput: "",
    };
  },
  methods: {
    updateInput(event) {
      //   this.$emit("update:modelValue", event.target.value);
      this.valueInput = event.target.value;
    },
    addTask() {
      if (this.valueInput.trim()) {
        this.unCompleteTasksList.push({
          body: this.valueInput,
          id: Math.random(),
          status: "need",
        });
        this.valueInput = "";
      }
    },
    tasksToHash() {
      this.allTasksList = this.unCompleteTasksList.concat(
        this.completeTasksList
      );
      let json = JSON.stringify(this.allTasksList);
      const encodedData = Base64.encode(json);
      window.location.hash = encodedData;
    },
  },
  watch: {
    unCompleteTasksList: {
      deep: true,
      handler() {
        this.tasksToHash();
      },
    },
    completeTasksList: {
      deep: true,
      handler() {
        this.tasksToHash();
      },
    },
  },
  mounted() {
    let decodedData = Base64.decode(window.location.hash.substring(1));
    if (decodedData) {
      let dataTasks = JSON.parse(decodedData);
      for (let i = 0; i < dataTasks.length; i++) {
        if (dataTasks[i].status === "need") {
          this.unCompleteTasksList.push(dataTasks[i]);
        } else {
          this.completeTasksList.push(dataTasks[i]);
        }
      }
    }
  },
};
</script>

<style lang="scss">
.wrapper {
  text-align: center;
  max-width: 1200px;
  font-family: "Open Sans", sans-serif;
  font-size: 1.3em;
}
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
.btn-add {
  margin: 0 10px;
  font-size: 19px;
  padding: 6px 16px;
  border-radius: 5px;
  background: none;
  color: #888;
  border: 1px solid #888;
  cursor: pointer;
}

/* Delete code */

// .all-tasks {
//   width: 80%;
//   margin: 0 auto;
//   .task {
//     display: flex;
//     gap: 10px;
//     margin: 0 auto;
//     margin-top: 1em;
//     font-size: 1.2em;
//   }
//   .title {
//     text-align: left;
//     font-weight: bold;
//     font-size: 2.2em;
//   }
//   .barrier {
//     position: relative;
//     display: flex;
//     justify-content: space-around;
//     margin: 20px 0 0;
//   }
//   .hr {
//     width: 80%;
//     border-bottom: 1px solid #ccc;
//     height: 13px;
//   }
//   .percent-completed {
//     font-size: 1rem;
//     display: block;
//     opacity: 0.6;
//   }
// }

// .completed-tasks {
//   .task--text {
//     text-decoration: line-through;
//   }
// }
</style>
