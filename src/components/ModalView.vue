<template>
  <div class="modal">
    <div class="overlay"></div>
    <div class="modal-card">
      <slot />
      <button
        class="px-4 py-2 font-bold text-white bg-blue-500 rounded hover:bg-blue-700"
        @click="emit('closeModal')"
      >
        close
      </button>
    </div>
  </div>
</template>

<script>
import useKeydown from '../composables/use-keydown'

export default {
  setup(props, { emit }) {
    useKeydown([
      {
        key: 'Enter',
        fn: () => {
          emit('closeModal')
        },
      },
    ])

    return {
      emit,
    }
  },
}
</script>
