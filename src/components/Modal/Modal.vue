<template>
  <div class="modal-overlay" v-if="isVisible">
    <div class="modal-content">
      <slot></slot>
      <button @click="close" class="modal-close">Fechar</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  props: {
    isVisible: {
      type: Boolean,
      required: true
    }
  },
  emits: ['close'],
  methods: {
    close() {
      this.$emit('close')
    }
  }
})
</script>

<style lang="scss" scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  width: 20rem;
  background: white;
  padding: 1.25rem;
  border-radius: 0.313rem;
  .modal-close {
    width: 5rem;
    border-radius: 0.5rem;
    background-color: #c01818;
    color: #e3e3e3;
    margin-left: 12rem;
    padding: 0.5rem;
    font-size: 0.813rem;
  }
}
</style>
