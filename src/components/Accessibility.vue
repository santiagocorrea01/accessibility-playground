<template>
  <section>
    <!-- Bad Code - no built in keyboard accessibility ( Has no semantic meanign ) -->
    <div>Play Video</div>

    <!-- Screen readers will read as a link, only use if it's actually going to redirect to a link  -->
    <a href="">Play Video</a>

    <!-- Good Code - Built-in keyboard accessibility -->
    <Modal identifier="24" label="Video">
      <p>Video would go here</p>
    </Modal>
  </section>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Modal from "./Modal.vue";

@Component({
  components: {
    Modal
  }
})
export default class Accessibility extends Vue {}
</script>
