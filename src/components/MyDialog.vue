<template>
  <Transition>
    <div class="modal-window-wrapper" v-show="show" @click="closeDialog">
      <div class="modal-window" @click.stop>
        <button class="close-modal-window" @click="closeDialog">&times;</button>
        <h2
          class="delete-task-text"
          :class="{ hiddenText: taskBody.length > 150 }"
        >
          Are you sure you want to delete task "{{ taskBody }}" ?
        </h2>
        <!-- <span class="question_mark">?</span> -->
        <button class="btn delete-task-yes" @click="$emit('removeTask')">
          Yes
        </button>
        <button class="btn delete-task-no" @click="closeDialog">No</button>
      </div>
    </div>
  </Transition>
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
    closeDialog(event) {
      this.$emit("closeDialog", event);
    },
  },
};
</script>

<style lang="scss" scoped>
.v-enter-active {
  // animation: fadeInModal 0.3s;
  opacity: 1;
  transition: 0.4s;
}
.v-leave-active {
  // animation: fadeOutModal 0.3s;
  opacity: 1;
  transition: 0.3s;
}
.v-enter-from,
.v-leave-to {
  opacity: 0;
}
@keyframes fadeOutModal {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
@keyframes fadeInModal {
  0% {
    opacity: 0;
  }
  25% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
// .nested-leave-active {
//   .modal-window {
//     animation: fadeOutModal 0.5s;
//   }
//   .overlay {
//     animation: fadeOutOverlay 0.5s;
//   }
// }

.modal-window-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(3px);
  z-index: 5;
}
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
    // &.delete_task_hidden {
    //   height: 140px;
    //   overflow: hidden;
    //   position: relative;
    //   &::after {
    //     content: "";
    //     position: absolute;
    //     left: 0;
    //     bottom: -3px;
    //     width: 100%;
    //     height: 40px;
    //     background: linear-gradient(180deg, transparent, #ccc 80%);
    //   }
    // }
    &.hiddenText {
      width: 90%;
      display: -webkit-inline-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
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
    // .delete-task-text.delete_task_hidden {
    //   height: 120px;
    // }
  }
  @media (max-width: 768px) {
    width: 80%;
    // .delete-task-text.delete_task_hidden {
    //   height: 100px;
    // }
    .btn {
      margin: 0.5em 0.5em 0;
    }
  }
  @media (max-width: 576px) {
    // .delete-task-text.delete_task_hidden {
    //   height: 85px;
    // }
    width: 90%;
    padding: 2rem;
    .close-modal-window {
      top: 0.5rem;
      right: 0;
    }
  }
}
</style>
