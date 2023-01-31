<template>
  <div class="modal-window-wrapper" v-show="show">
    <div class="modal-window">
      <button class="close-modal-window" @click="closeDialog">&times;</button>
      <h2
        class="delete-task-text"
        :class="{ delete_task_hidden: taskBody.length > 100 }"
      >
        Are you sure you want to delete task "<span
          :class="{ hiddenText: taskBody.length > 100 }"
          >{{ taskBody }}
        </span>
        " ?
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
    margin-bottom: 15px;
    &.delete_task_hidden {
      height: 140px;
      overflow: hidden;
      position: relative;
      &::after {
        content: "";
        position: absolute;
        left: 0;
        bottom: -3px;
        width: 100%;
        height: 40px;
        background: linear-gradient(180deg, transparent, #ccc 90%);
      }
    }
    // .hiddenText {
    //   display: -webkit-box;
    //   -webkit-line-clamp: 2;
    //   -webkit-box-orient: vertical;
    //   overflow: hidden;
    // }
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

  @media screen and (max-width: 1170px) {
    font-size: 1em;
    width: 50%;
    padding: 3rem;
    .close-modal-window {
      top: 1rem;
      right: 0.5rem;
      font-size: 3rem;
      line-height: 0.5;
    }
    .btn {
      font-size: 1.2rem;
    }
  }
  @media (max-width: 992px) {
    width: 70%;
    .delete-task-text.delete_task_hidden {
      height: 120px;
    }
  }
  @media (max-width: 768px) {
    width: 80%;
    .delete-task-text.delete_task_hidden {
      height: 110px;
    }
  }
  @media (max-width: 576px) {
    width: 90%;
  }
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
