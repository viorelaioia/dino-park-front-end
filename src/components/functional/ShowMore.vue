<template>
  <div :class="'show-more' + ( transition ? ' show-more--transition' : '') + ( isExpanded ? ' show-more--expanded' : '' )">
    <slot name="base">
    </slot>
    <transition v-if="overflowBefore" name="show-more__overflow-">
      <div class="show-more__overflow" v-if="isExpanded" tabindex="-1" ref="overflowContentElement">
        <slot name="overflow">
        </slot>
      </div>
    </transition>
    <button
      :class="'show-more__button ' + ( buttonClass ? ' ' + buttonClass : '')"
      type="button"
      :aria-expanded="isExpanded ? 'true' : 'false'"
      v-on:click="toggleOverflow">
        <template v-if="isExpanded">
          <slot name="icon-expanded"></slot>
          <span class="show-more__button-text">{{ alternateButtonText }}</span>
        </template>
        <template v-else>
          <slot name="icon-collapsed"></slot>
          <span class="show-more__button-text">{{ buttonText }}</span>
        </template>
        <slot name="button-content"></slot>
      </button>
     <transition v-if="overflowBefore === false" name="show-more__overflow-">
        <div class="show-more__overflow" v-if="isExpanded" tabindex="-1">
          <slot name="overflow">
          </slot>
        </div>
      </transition>
     </div>
</template>

<script>
export default {
  name: 'ShowMore',
  props: {
    buttonText: String,
    alternateButtonText: String,
    buttonClass: String,
    transition: Boolean,
    expanded: Boolean,
    closeWhenClickedOutside: {
      type: Boolean,
      default: false,
    },
    moveFocus: {
      type: Boolean,
      default: true,
    },
    overflowBefore: {
      type: Boolean,
      default: true,
    },
  },
  methods: {
    toggleOverflow(event) {
      this.isExpanded = !this.isExpanded;

      if (event.altKey) {
        this.$emit('expand-all');
      }
    },
    handleDocumentClick(event) {
      const expandedEl = this.$refs.overflowContentElement.firstElementChild;

      // closes overflow content if clicked anywhere, except the
      // overflowing content itself
      if (event.target !== expandedEl && expandedEl.contains(event.target) === false) {
        this.isExpanded = false;
      }
    },
  },
  data() {
    return {
      isExpanded: this.expanded,
    };
  },
  watch: {
    expanded() {
      this.isExpanded = this.expanded;
    },
  },
  updated() {
    const overflowContent = this.$refs.overflowContentElement;

    if (this.isExpanded && this.moveFocus) {
      overflowContent.focus();

      if (this.closeWhenClickedOutside) {
        document.addEventListener('click', this.handleDocumentClick);
        document.addEventListener('touchstart', this.handleDocumentClick);
      }
    } else if (this.closeWhenClickedOutside) {
      document.removeEventListener('click', this.handleDocumentClick);
      document.removeEventListener('touchstart', this.handleDocumentClick);
    }
  },
};
</script>

<style>
  .show-more {
    position: relative;
  }
  .show-more--transition .show-more__overflow--enter-active,
  .show-more--transition  .show-more__overflow--leave-active {
    transition: opacity .5s;
  }
  .show-more--transition .show-more__overflow--enter,
  .show-more--transition .show-more__overflow--leave-to {
    opacity: 0;
    z-index: 1;
  }
    .show-more__button {
      font: inherit;
    }
    .show-more__button > svg,
    .show-more__button > img {
      margin-right: 1.5em;
    }
    .show-more__button-text {
      font-size: .9em;
    }
    .show-more__button-text.contact-me__button {
      font-size: 1em;
    }
</style>

