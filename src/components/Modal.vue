<template>
  <div>
    <div class="c-modal__overlay" v-if="open"></div>
    <button @click="openModal">Open Modal</button>
    <!-- 
		Aria labels: Screen Reader Content  
		Aria Hidden: Should screen readers read or not?
		Aria-Modal: Additional Field to ensure functionality is read correctly
		role: Functionality
	-->
    <div
      :aria-label="label"
      :aria-hidden="open ? 'false' : 'true'"
      v-if="open"
      class="c-modal"
      ref="modal"
      role="dialog"
      aria-modal="true"
      :id="identifier"
      @keydown="checkKeyEvent"
    >
      <button @click="closeModal" aria-label="Close modal" v-focus>X</button>
      <slot></slot>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component({
  directives: {
    focus: {
      inserted: (el: HTMLElement) => el.focus(),
    },
  },
})
export default class Modal extends Vue {
  @Prop() public identifier!: string;
  @Prop() public label!: string;

  initialFocus!: HTMLElement;
  open = false;

  openModal(event: Event): void {
    this.initialFocus = event.target as HTMLElement;
    this.open = true;
    // Do not allow scrolling when modal is open
    document.body.classList.add("u-noscroll");
  }

  checkKeyEvent(event: KeyboardEvent): void {
    // close modal and return early if escape
    if (event.key === "Escape") {
      this.open = false;
      return;
    }

    const modal = this.$refs.modal as HTMLElement;

    const focusableList = modal.querySelectorAll<HTMLElement>(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );

    // escape early if only 1 or no elements to focus
    if (focusableList.length < 2 && event.key === "Tab") {
      event.preventDefault();
      return;
    }
    const last = focusableList.length - 1;
    if (
      event.key === "Tab" &&
      event.shiftKey === false &&
      event.target === focusableList[last]
    ) {
      event.preventDefault();
      focusableList[0].focus();
    } else if (
      event.key === "Tab" &&
      event.shiftKey === true &&
      event.target === focusableList[0]
    ) {
      event.preventDefault();
      focusableList[last].focus();
    }
  }

  closeModal(): void {
    this.open = false;
    this.initialFocus.focus();
    document.body.classList.remove("u-noscroll");
  }

  /* If I used a div instead of a button I would have to add a tabindex of 0 on the div and run something like this:

  document.onkeydown = function(e) {
    if(e.keyCode === 13) { // The Enter/Return key
      document.activeElement.click();
    }
  };

  */
}
</script>

<style lang="scss" scoped>
.c-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  padding: 32px;
  display: flex;
  flex-direction: column;

  &__overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: #000;
  }
}
</style>
