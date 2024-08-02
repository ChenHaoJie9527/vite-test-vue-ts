<template>
  <div class="responsive-layout flex flex-col h-screen">
    <!-- Sidebar -->
    <aside
      class="sidebar fixed left-0 top-0 h-full bg-blue-200 overflow-y-auto transition-all duration-300 ease-in-out z-30"
      :class="[
        sidebarVisible ? (sidebarExpanded ? 'w-60' : 'w-20') : 'w-0',
        'md:translate-x-0',
      ]"
    >
      <div class="flex justify-between items-center p-4">
        <h2
          class="text-xl font-bold"
          :class="{ 'sr-only': !sidebarExpanded && sidebarVisible }"
        >
          <slot name="sidebar-title">Sidebar</slot>
        </h2>
        <button @click="toggleSidebar" class="hidden md:block">
          <span class="text-2xl">{{ sidebarExpanded ? "←" : "→" }}</span>
        </button>
      </div>
      <slot name="sidebar-content"></slot>
    </aside>

    <!-- Overlay -->
    <div
      @click="dismissOverlay"
      class="overlay fixed inset-0 bg-black bg-opacity-50 transition-opacity duration-300 ease-in-out z-20"
      :class="{
        'opacity-100': showOverlay,
        'opacity-0 pointer-events-none': !showOverlay,
      }"
    ></div>

    <!-- Header -->
    <header
      class="header fixed top-0 left-0 right-0 h-16 bg-green-200 z-10 transition-all duration-300 ease-in-out"
      :class="{
        'md:ml-60': sidebarVisible && sidebarExpanded,
        'md:ml-20': sidebarVisible && !sidebarExpanded,
      }"
    >
      <div class="flex items-center h-full px-4">
        <button @click="toggleSidebar" class="md:hidden mr-4">
          <span class="text-2xl">☰</span>
        </button>
        <slot name="header-content">
          <h1 class="text-2xl font-bold">Header</h1>
        </slot>
      </div>
    </header>

    <!-- Main content -->
    <main
      class="main-content flex-grow pt-16 bg-yellow-100 overflow-y-auto transition-all duration-300 ease-in-out"
      :class="{
        'md:ml-60': sidebarVisible && sidebarExpanded,
        'md:ml-20': sidebarVisible && !sidebarExpanded,
      }"
    >
      <div class="p-4">
        <slot name="main-content">
          <h2 class="text-xl font-bold mb-4">Main Content</h2>
          <p>This is the main content area. It will scroll independently.</p>
        </slot>
      </div>
    </main>

    <!-- Footer -->
    <footer
      class="footer bg-red-200 transition-all duration-300 ease-in-out"
      :class="{
        'md:ml-60': sidebarVisible && sidebarExpanded,
        'md:ml-20': sidebarVisible && !sidebarExpanded,
      }"
    >
      <div class="p-4">
        <slot name="footer-content">
          <p>Footer content</p>
        </slot>
      </div>
    </footer>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onUnmounted, watch } from "vue";

export default defineComponent({
  name: "ResponsiveLayout",
  props: {
    initialSidebarExpanded: {
      type: Boolean,
      default: true,
    },
  },
  emits: ["update:sidebarVisible", "update:sidebarExpanded"],
  setup(props, { emit }) {
    const sidebarVisible = ref(false);
    const sidebarExpanded = ref(props.initialSidebarExpanded);
    const showOverlay = ref(false);
    const isInOverlayRange = ref(false);

    const toggleSidebar = () => {
      const innerWidth = window.innerWidth
      console.log('innerWidth =>', innerWidth);
      if (innerWidth < 768) {
        sidebarVisible.value = !sidebarVisible.value;
        showOverlay.value = true;
        emit("update:sidebarVisible", sidebarVisible.value);
      } else {
        sidebarExpanded.value = !sidebarExpanded.value;
        emit("update:sidebarExpanded", sidebarExpanded.value);
      }
    };

    const dismissOverlay = () => {
      showOverlay.value = false;
      if (window.innerWidth < 768) {
        sidebarVisible.value = false;
        emit("update:sidebarVisible", false);
      }
    };

    const checkScreenSize = () => {
      const width = window.innerWidth;
      isInOverlayRange.value = width >= 768 && width < 1024;
      if (width >= 1024) {
        sidebarVisible.value = true;
        sidebarExpanded.value = true;
        showOverlay.value = false;
      } else if (width >= 768) {
        sidebarVisible.value = true;
        showOverlay.value = true;
      } else {
        sidebarVisible.value = false;
        showOverlay.value = false;
      }

      emit("update:sidebarVisible", sidebarVisible.value);
      emit("update:sidebarExpanded", sidebarExpanded.value);
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
      } else if (sidebarVisible.value) {
        showOverlay.value = true;
      }
    });

    return {
      sidebarVisible,
      sidebarExpanded,
      showOverlay,
      toggleSidebar,
      dismissOverlay,
    };
  },
});
</script>

<style scoped>
.responsive-layout {
  /* You can add any global styles for the layout here */
}
</style>
