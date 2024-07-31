<script setup lang="ts">
import { ref, computed } from 'vue';

const isCollapsed = ref(false);
const sidebarWidth = computed(() => isCollapsed.value ? '80px' : '200px');

const toggleSidebar = () => {
  isCollapsed.value = !isCollapsed.value;
};
</script>

<template>
  <div class="app-container">
    <el-container>
      <el-aside class="sidebar" :width="sidebarWidth">
        <el-button @click="toggleSidebar" class="toggle-btn">
          {{ isCollapsed ? '>' : '<' }}
        </el-button>
        <div class="sidebar-content" :class="{ 'collapsed': isCollapsed }">
          Sidebar Content
        </div>
      </el-aside>
      <el-container class="main-container">
        <el-header class="header">Fixed Header</el-header>
        <el-main class="main-content">
          <div class="scrollable-content">
            <h2>Main Content</h2>
            <p v-for="n in 50" :key="n">This is paragraph {{ n }}</p>
          </div>
        </el-main>
        <el-footer>Footer</el-footer>
      </el-container>
    </el-container>
  </div>
</template>

<style lang="scss" scoped>
.app-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;

  .el-container {
    height: 100%;
  }

  .sidebar {
    background: blueviolet;
    transition: width 0.3s;
    overflow: hidden;

    .toggle-btn {
      
    }

    .sidebar-content {
      width: 200px;
      transition: width 0.3s;
      padding: 20px;
      color: white;

      &.collapsed {
        width: 80px;
        padding: 20px 0;
        text-align: center;
      }
    }
  }

  .main-container {
    flex-direction: column;
    overflow: hidden;
  }

  .header {
    background-color: palevioletred;
    position: sticky;
    top: 0;
    z-index: 1000;
  }

  .main-content {
    overflow-y: auto;
    flex: 1;
  }

  .scrollable-content {
    padding: 20px;
  }
}
</style>