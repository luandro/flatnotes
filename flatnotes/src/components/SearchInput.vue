<template>
  <form v-on:submit.prevent="search" class="w-100">
    <div class="input-group w-100">
      <input
        id="search-input"
        type="text"
        inputmode="search"
        class="form-control"
        :class="{ highlight: includeHighlightClass }"
        placeholder="Search"
        v-model="searchTermInput"
      />
      <div class="input-group-append">
        <button class="btn" type="submit">
          <b-icon icon="search"></b-icon>
        </button>
      </div>
    </div>
  </form>
</template>

<style lang="scss" scoped>
@import "../colours";

@keyframes highlight {
  from {
    background-color: $input-highlight;
  }
  to {
    background-color: white;
  }
}

.highlight {
  animation-name: highlight;
  animation-duration: 1.5s;
}

.btn {
  border: 1px solid $form-control-border;
  svg {
    color: $muted-text;
  }
}
</style>

<script>
import * as constants from "../constants";

import EventBus from "../eventBus";

export default {
  props: { initialValue: { type: String } },

  data: function () {
    return {
      searchTermInput: null,
      includeHighlightClass: false,
    };
  },

  watch: {
    initialValue: function () {
      this.init();
    },
  },

  methods: {
    search: function () {
      if (this.searchTermInput) {
        this.searchTermInput = this.searchTermInput.trim();
      }
      if (this.searchTermInput) {
        EventBus.$emit(
          "navigate",
          `${constants.basePaths.search}?${
            constants.params.searchTerm
          }=${encodeURIComponent(this.searchTermInput)}`
        );
      } else {
        this.$bvToast.toast("Please enter a search term ✘", {
          variant: "danger",
          noCloseButton: true,
          toaster: "b-toaster-bottom-right",
        });
      }
    },

    highlightSearchInput: function () {
      let parent = this;
      this.includeHighlightClass = true;
      setTimeout(function () {
        parent.includeHighlightClass = false;
      }, 1500);
    },

    init: function () {
      this.searchTermInput = this.initialValue;
    },
  },

  created: function () {
    EventBus.$on("highlight-search-input", this.highlightSearchInput);
    this.init();
  },
};
</script>
