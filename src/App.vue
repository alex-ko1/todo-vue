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
</template>

<script>
import MyInput from "./components/MyInput.vue";
import TaskList from "./components/TaskList.vue";
import TaskItem from "./components/TaskItem.vue";
import { Base64 } from "https://cdn.jsdelivr.net/npm/js-base64@3.7.4/base64.mjs";

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
    // Function to add a task to the to-do list.
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
    // Encode the array of all tasks into hash.
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
    // Decode tasks from hash
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
</style>
