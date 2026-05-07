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
    
    <template #footer>
        <!-- 空模板以彻底移除页面底部默认的 footer -->
    </template>
  </Layout>
</template>

<style>
/* 彻底消除页面最右侧的浏览器全局滚动条，锁定全屏应用状态 */
html,
body,
.theme-container,
#app {
  overflow: hidden !important;
  height: 100vh !important;
}

/* 彻底隐藏当前页面（布局）下默认的页脚 footer */
.vp-footer,
.footer-wrapper,
.footer {
  display: none !important;
}

/* 确保内容区域在主题布局内正确显示，并完美同步 VuePress 侧边栏与顶部导航栏的避让逻辑 */
.ai-assistant-page {
  height: calc(100vh - var(--navbar-height, 3.75rem)); 
  margin-top: var(--navbar-height, 3.75rem); /* 避让顶部的 position: fixed 导航栏 */
  padding: 16px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

@media (min-width: 960px) {
  .ai-assistant-page {
    height: calc(100vh - var(--navbar-height, 3.75rem));
    margin-top: var(--navbar-height, 3.75rem);
    padding: 24px;
    padding-left: calc(var(--sidebar-width, 20rem) + 24px);
  }
}
</style>
