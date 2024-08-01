<template>
  <div class="flex flex-col h-screen">
    <!-- Sidebar -->
    <div
      class="fixed left-0 top-0 h-full bg-blue-200 overflow-y-auto transition-all duration-300 ease-in-out z-30"
      :class="{ 'w-60': sidebarVisible, 'w-0': !sidebarVisible }"
    >
      <h2 class="text-xl font-bold p-4">Sidebar</h2>
      <!-- Add sidebar content here -->
    </div>

    <!-- Overlay -->
    <div
      @click="dismissOverlay"
      class="fixed inset-0 bg-black bg-opacity-50 transition-opacity duration-300 ease-in-out z-20"
      :class="{
        'opacity-100': showOverlay,
        'opacity-0 pointer-events-none': !showOverlay,
      }"
    ></div>

    <!-- Header -->
    <header
      class="fixed top-0 left-0 right-0 h-16 bg-green-200 z-10 transition-all duration-300 ease-in-out"
      :class="{ 'ml-60': sidebarVisible }"
    >
      <div class="flex items-center h-full px-4">
        <button @click="toggleSidebar" class="mr-4">
          <span class="text-2xl">☰</span>
        </button>
        <h1 class="text-2xl font-bold">Header</h1>
      </div>
    </header>

    <!-- Main content -->
    <main
      class="flex-grow pt-16 bg-yellow-100 overflow-y-auto transition-all duration-300 ease-in-out"
      :class="{ 'ml-60': sidebarVisible }"
    >
      <div class="p-4">
        <h2 class="text-xl font-bold mb-4">Main Content</h2>
        <p>This is the main content area. It will scroll independently.</p>
        <!-- Add more content here to demonstrate scrolling -->
      </div>
    </main>

    <!-- Footer -->
    <footer
      class="bg-red-200 transition-all duration-300 ease-in-out"
      :class="{ 'ml-60': sidebarVisible }"
    >
      <div class="p-4">
        <p>Footer content</p>
      </div>
    </footer>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onUnmounted, watch } from "vue";

export default defineComponent({
  name: "ResponsiveLayout",
  setup() {
    const sidebarVisible = ref(false);
    const showOverlay = ref(false);
    const isInOverlayRange = ref(false);

    const toggleSidebar = () => {
      sidebarVisible.value = !sidebarVisible.value;
      if (isInOverlayRange.value) {
        showOverlay.value = sidebarVisible.value;
      }
    };

    const dismissOverlay = () => {
      showOverlay.value = false;
      if (isInOverlayRange.value) {
        sidebarVisible.value = false;
      }
    };

    const checkScreenSize = () => {
      const width = window.innerWidth;
      isInOverlayRange.value = width >= 768 && width < 1024;

      if (width >= 1024) {
        sidebarVisible.value = true;
        showOverlay.value = false;
      } else if (width < 768) {
        sidebarVisible.value = false;
        showOverlay.value = false;
      } else {
        sidebarVisible.value = true;
        showOverlay.value = true;
      }
    };

    onMounted(() => {
      checkScreenSize();
      window.addEventListener("resize", checkScreenSize);
    });

    onUnmounted(() => {
      window.removeEventListener("resize", checkScreenSize);
    });

    watch(isInOverlayRange, (newValue) => {
      if (!newValue) {
        showOverlay.value = false;
      }
    });

    return {
      sidebarVisible,
      showOverlay,
      toggleSidebar,
      dismissOverlay,
    };
  },
});
</script>

<style scoped>
/* Additional styles can be added here if needed */
</style>
