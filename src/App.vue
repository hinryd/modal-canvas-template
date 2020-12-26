<template>
  <div id="app">
    <ToggleButton :stateToToggle="modalState" @toggleState="toggleModal" />
    <Modal v-if="modalState" @close="toggleModal(false)" />
  </div>
</template>

<script>
import ToggleButton from "./components/ToggleButton";
import Modal from "./components/Modal";

export default {
  name: "App",
  components: {
    ToggleButton,
    Modal,
  },
  data() {
    return { modalState: false };
  },
  methods: {
    toggleModal: function(e) {
      this.modalState = e;
    },
  },
};
</script>
