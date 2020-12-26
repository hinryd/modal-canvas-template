<template>
  <div>
    <button @click="toggleState">
      {{ stateToToggle }}
    </button>
  </div>
</template>

<script>
export default {
  name: "ToggleButton",
  props: {
    stateToToggle: Boolean,
  },
  methods: {
    toggleState: function() {
      this.$emit("toggleState", !this.stateToToggle);
    },
  },
};
</script>
