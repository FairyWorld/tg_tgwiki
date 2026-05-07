<script setup>
// 从 vuepress-theme-hope 的客户端库导入 Layout 组件
import { Layout } from "vuepress-theme-hope/client";
import AiAssistant from '../components/aiAssistant.vue' 
</script>

<template>
  <Layout>
    <template #default>
        <div class="ai-assistant-page">
            <AiAssistant />
        </div>
    </template>
    
    </Layout>
</template>

<style>
/* 确保内容区域在主题布局内正确显示，并完美同步 VuePress 侧边栏与顶部导航栏的避让逻辑 */
.ai-assistant-page {
  min-height: 80vh; 
  padding: calc(var(--navbar-height, 3.75rem) + 2rem) 1rem 2rem 1rem;
  box-sizing: border-box;
}

@media (min-width: 960px) {
  .ai-assistant-page {
    /* 在宽屏桌面端，强制加上左边距和顶部距离，避让侧边栏与顶部栏 */
    padding: calc(var(--navbar-height, 3.75rem) + 2rem) 2.5rem 2rem calc(var(--sidebar-width, 20rem) + 2.5rem);
  }
}
</style>
