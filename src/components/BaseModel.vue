<template>
  <teleport to="body">
    <Transition name="model-outer">
      <div
        v-show="modelActive"
        class="absolute w-full bg-black bg-opacity-30 flex justify-center h-screen top-0 left-0 px-8"
      >
        <transition name="model-inner">
          <div
            v-if="modelActive"
            class="p-4 bg-white self-start mt-32 max-w-screen-md"
          >
            <slot />
            <button
              class="text-white mt-8 bg-weather-primary py-2 px-6"
              @click="$emit('close-model')"
            >
              Close
            </button>
          </div>
        </transition>
      </div>
    </Transition>
  </teleport>
</template>

<script setup>
defineEmits(["close-model"]);
defineProps({
  modelActive: {
    type: Boolean,
    default: false,
  },
});
</script>
<style scoped>
.model-outer-enter-active,
.model-outer-leave-active {
  transition: opacity 0.5s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}
.model-outer-enter-from,
.model-outer-leave-to {
  opacity: 0;
}

.model-inner-enter-active {
  transition: all 0.5s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
}
.model-inner-leave-active {
  transition: all 0.5s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}
.model-inner-enter-from {
  opacity: 0;
  transform: scale(0.8);
}
.model-inner-leave-to {
  transform: scale(0.8);
}
</style>