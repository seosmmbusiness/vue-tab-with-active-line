<template>
  <nav class="tabs" :class="wrapperClass">
    <button
      v-for="tab in tabs"
      :ref="tab.value"
      :key="tab.title"
      class="tabs__item"
      type="button"
      :class="[
        { tabs__item_active: tab.value === currentTab },
        tab.value === currentTab && tabActiveClass ? tabActiveClass : '',
        tabClass
      ]"
      :disabled="tab.disabled || false"
      @click="handleClick(tab.value)"
    >
      {{ tab.title }}
    </button>

    <div
      class="tabs__active-line"
      :class="lineClass"
      :style="{
        width: `${activeLineWidth}px`,
        transform: `translateX(${activeLineOffset}px)`
      }"
    />
  </nav>
</template>

<script>
export default {
  props: {
    currentTab: {
      type: String,
      required: true
    },
    tabs: {
      type: Array,
      required: true
    },
    updated: {
      type: [Boolean, String, Array],
      default: undefined
    },
    wrapperClass: {
      type: String,
      default: 'default-tabs',
      required: false
    },
    tabClass: {
      type: String,
      default: 'default-tabs__item',
      required: false
    },
    tabActiveClass: {
      type: String,
      default: 'default-tabs__item_active',
      required: false
    },
    lineClass: {
      type: String,
      default: 'default-tabs__active-line',
      required: false
    }
  },
  data: () => ({
    activeLineWidth: 0,
    activeLineOffset: 0,
    newTab: ''
  }),
  watch: {
    currentTab(newCurrentTab) {
      if (this.newTab === newCurrentTab) return
      this.moveActiveLine(newCurrentTab)
      this.newTab = newCurrentTab
    },
    updated() {
      this.moveActiveLine(this.currentTab)
    }
  },
  mounted() {
    this.moveActiveLine(this.currentTab)
    this.newTab = this.currentTab
    window.addEventListener('resize', this.onResize)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.onResize)
  },
  methods: {
    handleClick(value) {
      this.$emit('onClick', value)
      this.moveActiveLine(value)
      this.newTab = value
    },
    onResize() {
      this.moveActiveLine(this.currentTab)
    },
    moveActiveLine(newValue) {
      if (!this.currentTab) return

      if (!this.$refs || !this.$refs[newValue] || !this.$refs[newValue][0])
        return
      const element = this.$refs[newValue][0]

      this.activeLineWidth = element.clientWidth
      this.activeLineOffset = element.offsetLeft
    }
  }
}
</script>

<style lang="postcss" scoped>
.default-tabs {
  @apply relative m-auto;

  &__item {
    @apply inline-block pb-2 px-3 text-black border-0 border-b-2 border-transparent bg-transparent cursor-pointer transition-all no-underline;

    &_active {
      @apply text-white;
    }
    &:hover {
      @apply text-white border-b-2;
    }
    &:focus {
      @apply text-white border-b-2;
    }
    &:first-child {
      @apply ml-0;
    }
    &:last-child {
      @apply mr-0;
    }
  }
  &__active-line {
    @apply absolute bottom-0 left-0 h-0.5 bg-red;

    transition: transform 0.4s ease, width 0.4s ease;
  }
}
</style>
