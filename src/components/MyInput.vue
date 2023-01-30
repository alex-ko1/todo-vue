<template>
  <input type="text" class="input-new-task" placeholder="New task..." />
</template>

<script>
export default {
  data() {
    return {
      task: {
        body: "",
      },
    };
  },
  name: "my-input",
  props: {
    modelValue: [String, Number],
  },
  methods: {},
};
</script>

<style scoped lang="scss">
.input-new-task {
  width: 70%;
  margin: 20px 0;
  height: 22px;
  font-size: 21px;
  background-color: transparent;
  padding: 14px 10px;
  outline: none;
  border: none;
  border-bottom: 2px dashed var(--vt-c-divider-dark-2);
  //color: rgba(235, 235, 235, 0.64);
  color: var(--color-text);
  // &:focus {
  //   border-bottom: 2px solid #000;
  // }
}
</style>
