<template>
  <div class="modal-window-wrapper" v-show="show">
    <div class="modal-window">
      <button class="close-modal-window" @click="closeDialog">&times;</button>
      <h2 class="delete-task-text">
        Are you sure you want to delete task "<span
          :class="{ hiddenText: taskBody.length > 100 }"
          >{{ taskBody }}</span
        >
        "?
      </h2>
      <button class="btn delete-task-yes" @click="$emit('removeTask')">
        Yes
      </button>
      <button class="btn delete-task-no" @click="closeDialog">No</button>
    </div>
    <div class="overlay" @click="closeDialog"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  name: "my-dialog",
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    taskBody: {
      type: String,
    },
  },
  methods: {
    closeDialog() {
      this.$emit("closeDialog");
    },
  },
};
</script>

<style lang="scss" scoped>
.modal-window {
  .close-modal-window {
    position: absolute;
    top: 1.2rem;
    right: 2rem;
    font-size: 5rem;
    color: #444;
    cursor: pointer;
    border: none;
    background: none;
  }
  .delete-task-text {
    .hiddenText {
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
    }
    @media screen and (max-width: 1170px) {
      font-size: 1em;
    }
  }
  .btn {
    font-size: 1.4rem;
    margin: 0 1em;
    padding: 2px 15px;
    cursor: pointer;
    &.delete-task-yes {
      color: #fff;
      background-color: #c82333;
      border: 1px solid transparent;
      border-radius: 0.25rem;
    }
  }
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 50%;
  background-color: rgb(255, 255, 255, 0.5);
  padding: 6rem;
  border-radius: 5px;
  box-shadow: 0 3rem 5rem rgba(0, 0, 0, 0.3);
  z-index: 10;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(3px);
  z-index: 5;
}
</style>
