<template>
  <div class="task">
    <input
      type="checkbox"
      id="checkbox"
      class="checkbox"
      :class="{ hidden: isShowInput }"
      @change="isChecked"
      :checked="checked"
    />
    <p class="task--text" @dblclick="dbclickFunc" v-if="!isShowInput">
      {{ task.body }}
    </p>
    <input
      v-else
      v-model="task.body"
      ref="taskBody"
      class="task--text-input"
      @keypress.enter="changeTaskBody"
      placeholder="Change task..."
    />
    <button class="dump" :class="{ hidden: isShowInput }" @click="showDialog">
      🗑
    </button>
    <my-dialog
      :show="isShow"
      :taskBody="task.body"
      @closeDialog="isShow = !isShow"
      @removeTask="$emit('removeTask')"
    />
  </div>
</template>

<script>
import { ref, toRaw } from "@vue/reactivity";
import MyDialog from "./MyDialog.vue";

export default {
  components: { MyDialog },
  data() {
    return {
      isShow: false,
      isShowInput: false,
    };
  },
  emits: ["isChecked", "removeTask"],
  props: {
    task: {
      type: Object,
      required: true,
    },
    checked: {
      type: Boolean,
    },
  },
  methods: {
    isChecked(event) {
      this.$emit("isChecked", event);
    },

    // Func for show modal window.
    showDialog() {
      this.isShow = !this.isShow;
    },

    // Double-click the task body to change it.
    dbclickFunc() {
      if (this.task.status === "need") {
        this.isShowInput = true;
        this.$nextTick(() => {
          // This callback will only be called after the
          // DOM has been updated
          this.$refs.taskBody.focus();
        });
      }
    },

    // Press enter if you wanna to save changes in task body
    changeTaskBody() {
      if (this.task.body.trim()) {
        this.isShowInput = false;
      }
    },
  },
};
</script>

<style scoped lang="scss">
.task {
  width: 100%;
  display: flex;
  gap: 10px;
  margin: 1em auto 0;
  font-size: 1.2em;
  word-break: break-all;
  .task--text-input {
    outline: none;
    border: 1px solid var(--vt-c-divider-dark-2);
    border-radius: 5px;
    background: none;
    width: 100%;
    color: var(--color-text);
    font-size: 1em;
  }
}
.dump {
  background-color: transparent;
  border: none;
  cursor: pointer;
  height: max-content;
  margin: auto 0 auto auto;
  font-size: 1.1em;
}
.completed-tasks .task--text {
  text-decoration: line-through;
}
input.checkbox {
  width: 1.3em;
  height: 1.3em;
  margin: auto 0;
  background-color: white;
  color: dodgerblue;
  border-radius: 50%;
  vertical-align: middle;
  border: 1px solid #ddd;
  appearance: none;
  -webkit-appearance: none;
  outline: none;
  cursor: pointer;
  vertical-align: middle;
  -webkit-appearance: none;
  background: none;
  border: 0;
  outline: 0;
  flex-grow: 0;
  transition: background 300ms;
  &::before {
    content: "";
    color: transparent;
    display: block;
    width: inherit;
    height: inherit;
    border-radius: inherit;
    border: 0;
    background-color: transparent;
    background-size: contain;
    box-shadow: inset 0 0 0 1px #ccd3d8;
  }
  &:checked {
    background-color: #008436;
    background-color: currentcolor;
    &::before {
      box-shadow: none;
      background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24'%3E %3Cpath d='M15.88 8.29L10 14.17l-1.88-1.88a.996.996 0 1 0-1.41 1.41l2.59 2.59c.39.39 1.02.39 1.41 0L17.3 9.7a.996.996 0 0 0 0-1.41c-.39-.39-1.03-.39-1.42 0z' fill='%23fff'/%3E %3C/svg%3E");
    }
  }
}
.completed-tasks .task--text {
  opacity: 0.5;
}
.hidden {
  visibility: hidden;
}
</style>
