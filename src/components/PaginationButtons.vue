<template>
  <div class="pagination-container">
    <div class="round-ball green" @click="previousPage">
      <i class="fa-solid fa-chevron-left"></i>
    </div>
    <div class="round-ball yellow">{{ page }}</div>
    <div class="round-ball green" @click="nextPage"><i class="fa-solid fa-chevron-right"></i></div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    page: {
      required: true,
      type: Number
    }
  },
  methods: {
    nextPage() {
      this.$emit('next')
    },
    previousPage() {
      this.$emit('previous')
    }
  }
})
</script>

<style lang="scss" scoped>
.pagination-container {
  width: 100%;
  display: flex;
  justify-content: center;
  margin: 1rem 0;
  font-size: 1.25rem;

  .round-ball {
    cursor: pointer;
    width: 3rem;
    height: 3rem;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 0.125rem solid #000;
    font-weight: 700;
    border-radius: 100%;
    color: #000;

    &.green {
      background: #3ec70b;
    }
    &.yellow {
      background: #f7ec09;
      margin: 0 1rem;
    }
  }
}
</style>
