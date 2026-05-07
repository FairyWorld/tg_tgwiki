<script setup>
import { ref } from 'vue'

// !!! 请将此地址替换为你真实的 AI API 地址 !!!
const AI_API_URL = 'https://wikiapi.tgnav.org/ask'

const question = ref('')
const conversation = ref([
  { role: 'assistant', text: '您好！我是TGwikiAI，由TGwiki根据文档内容训练的AI助手。请问有什么可以帮助到您？', isAI: false }
])
const isLoading = ref(false)

/**
 * 发送用户问题到 AI API
 */
const submitQuestion = async () => {
  const userQuestion = question.value.trim()
  
  if (!userQuestion || isLoading.value) return

  // 1. 重置输入框，标记加载状态
  question.value = ''
  isLoading.value = true

  // 2. 添加用户问题到对话记录
  conversation.value.push({ role: 'user', text: userQuestion })

  try {
    // 3. 构造 POST 请求体
    const requestBody = {
      question: userQuestion, 
    }
    
    // 4. 发送 POST 请求
    const response = await fetch(AI_API_URL, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(requestBody),
    })

    // 5. 检查响应状态
    if (!response.ok) {
      throw new Error(`API错误：${response.status} ${response.statusText}`)
    }

    // 6. 解析 JSON 响应
    const data = await response.json()
    
    // 假设 AI 接口返回的回答内容在 data.answer 字段中
    const aiAnswer = data.answer || '出现问题：服务接口返回的数据格式不正确。'

    // 7. 添加 AI 回答到对话记录
    conversation.value.push({ role: 'assistant', text: aiAnswer, isAI: true })
    
  } catch (error) {
    console.error('AI助手请求出错：', error)
    // 8. 处理错误，并提示用户
    conversation.value.push({ 
      role: 'assistant', 
      text: `抱歉，尝试连接TGwikiAI时出错。错误信息：${error.message}。请检查您的网络环境或稍后重试。`,
      isAI: false
    })
  } finally {
    // 9. 移除加载状态
    isLoading.value = false
    scrollToBottom()
  }
}

const formatMessage = (text) => {
  if (!text) return ''
  const trimmedText = text.trim()

  const escapeHtml = (str) => {
    return str
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
  }

  const parseInline = (str) => {
    let res = escapeHtml(str)

    // Bold: **text** or __text__
    res = res.replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>')
    res = res.replace(/__([^_]+)__/g, '<strong>$1</strong>')

    // Italic: *text* or _text_
    res = res.replace(/\*([^*]+)\*/g, '<em>$1</em>')
    res = res.replace(/_([^_]+)_/g, '<em>$1</em>')

    // Inline code: `code`
    res = res.replace(/`([^`]+)`/g, '<code class="inline-code">$1</code>')

    // Links: [text](url)
    res = res.replace(/\[([^\]]+)\]\(([^)]+)\)/g, '<a href="$2" target="_blank" rel="noopener noreferrer" class="markdown-link">$1</a>')

    return res
  }

  const parseMarkdownBlocks = (blockText) => {
    const lines = blockText.split('\n')
    const result = []
    let inList = false
    let listType = '' // 'ul' or 'ol'

    for (let i = 0; i < lines.length; i++) {
      const line = lines[i]

      // Check lists
      const ulMatch = line.match(/^\s*[-*+]\s+(.+)$/)
      const olMatch = line.match(/^\s*(\d+)\.\s+(.+)$/)

      if (ulMatch) {
        if (!inList || listType !== 'ul') {
          if (inList) result.push(`</${listType}>`)
          result.push('<ul class="markdown-ul">')
          inList = true
          listType = 'ul'
        }
        result.push(`<li class="markdown-li">${parseInline(ulMatch[1])}</li>`)
        continue
      }

      if (olMatch) {
        if (!inList || listType !== 'ol') {
          if (inList) result.push(`</${listType}>`)
          result.push('<ol class="markdown-ol">')
          inList = true
          listType = 'ol'
        }
        result.push(`<li class="markdown-li">${parseInline(olMatch[2])}</li>`)
        continue
      }

      // If in list but line is not a list item, close list
      if (inList) {
        result.push(`</${listType}>`)
        inList = false
        listType = ''
      }

      // Empty line
      if (line.trim() === '') {
        continue
      }

      // Headers
      const headerMatch = line.match(/^(#{1,6})\s+(.+)$/)
      if (headerMatch) {
        const level = headerMatch[1].length
        result.push(`<h${level} class="markdown-h${level}">${parseInline(headerMatch[2])}</h${level}>`)
        continue
      }

      // Standard paragraph
      result.push(`<p class="markdown-para">${parseInline(line)}</p>`)
    }

    if (inList) {
      result.push(`</${listType}>`)
    }

    return result.join('\n')
  }

  // Split by code blocks to prevent parsing markdown inside code blocks
  const parts = trimmedText.split('```')
  let htmlResult = ''
  for (let i = 0; i < parts.length; i++) {
    if (i % 2 === 1) {
      // Inside code block
      const part = parts[i]
      const firstNewline = part.indexOf('\n')
      let lang = ''
      let code = part
      if (firstNewline !== -1) {
        lang = part.substring(0, firstNewline).trim()
        code = part.substring(firstNewline + 1)
      }
      htmlResult += `<pre class="code-block${lang ? ' lang-' + lang : ''}"><code>${escapeHtml(code.trim())}</code></pre>`
    } else {
      // Outside code block
      htmlResult += parseMarkdownBlocks(parts[i])
    }
  }

  return htmlResult
}

/**
 * 辅助函数：滚动到对话区域底部
 */
const scrollToBottom = () => {
  setTimeout(() => {
    const container = document.querySelector('.conversation-area')
    if (container) {
      // 确保滚动行为平滑且在 DOM 更新后执行
      container.scrollTop = container.scrollHeight
    }
  }, 0)
}
</script>

<template>
  <div class="ai-assistant-container">
    <div class="assistant-header">
      <div class="header-icon">🤖</div>
      <div class="header-info">
        <h1 class="assistant-title">TGwikiAI [测试版]</h1>
        <p class="assistant-subtitle">基于TGwiki文档内容训练的智能助手</p>
      </div>
    </div>
    
    <div class="conversation-area">
      <div 
        v-for="(msg, index) in conversation" 
        :key="index" 
        :class="['message-wrapper', msg.role === 'user' ? 'user-wrapper' : 'assistant-wrapper']"
      >
        <div class="avatar" :class="msg.role === 'user' ? 'user-avatar' : 'assistant-avatar'">
          {{ msg.role === 'user' ? '👤' : '🤖' }}
        </div>
        <div class="message-bubble-container">
          <div class="message-bubble" :class="msg.role === 'user' ? 'user-bubble' : 'assistant-bubble'">
            <div v-html="formatMessage(msg.text)" class="markdown-content"></div>
          </div>
          <em v-if="msg.role === 'assistant' && msg.isAI" class="ai-disclaimer">
            <svg class="info-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <circle cx="12" cy="12" r="10"></circle>
              <line x1="12" y1="16" x2="12" y2="12"></line>
              <line x1="12" y1="8" x2="12.01" y2="8"></line>
            </svg>
            内容由 AI 生成，请仔细甄别
          </em>
        </div>
      </div>
      
      <!-- ChatGPT style thinking/loading state -->
      <div v-if="isLoading" class="message-wrapper assistant-wrapper loading-wrapper">
        <div class="avatar assistant-avatar">🤖</div>
        <div class="message-bubble-container">
          <div class="message-bubble assistant-bubble loading-bubble">
            <div class="thinking-process">
              <div class="thinking-spinner"></div>
              <span class="thinking-label">正在查阅知识库并思考...</span>
            </div>
            <div class="typing-indicator">
              <span></span>
              <span></span>
              <span></span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="input-container">
      <div class="input-wrapper">
        <input 
          v-model="question" 
          @keyup.enter="submitQuestion" 
          :disabled="isLoading"
          placeholder="向 TGwikiAI 提问..."
        />
        <button 
          @click="submitQuestion" 
          :disabled="isLoading || !question.trim()" 
          class="send-button"
          aria-label="发送"
        >
          <svg class="send-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
            <line x1="22" y1="2" x2="11" y2="13"></line>
            <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
          </svg>
        </button>
      </div>
      <div class="input-footer">
        TGwikiAI 可能会犯错。请核查重要信息。
      </div>
    </div>
  </div>
</template>

<style scoped>
/* ==================== 默认亮色模式 (Light Mode) ==================== */
.ai-assistant-container,
.ai-assistant-container * {
  box-sizing: border-box;
}

.ai-assistant-container {
  --theme-color-resolved: var(--theme-color, var(--vp-c-accent, var(--vp-c-brand, var(--c-brand, #3eaf7c))));
  max-width: 800px;
  width: 100%;
  height: 100%;
  flex: 1;
  min-height: 0;
  margin: 0 auto;
  padding: 12px;
  border: 1px solid #e2e8f0;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.04);
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
  gap: 10px;
  transition: all 0.3s ease;
}

/* 头部样式 */
.assistant-header {
  display: flex;
  align-items: center;
  gap: 16px;
  padding-bottom: 16px;
  border-bottom: 1px solid #e2e8f0;
}

.header-icon {
  font-size: 32px;
  background-color: #f8fafc;
  width: 52px;
  height: 52px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
  border: 1px solid #e2e8f0;
}

.header-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.assistant-title {
  font-size: 20px;
  font-weight: 700;
  color: #1e293b;
  margin: 0;
  text-align: left;
}

.assistant-subtitle {
  font-size: 13px;
  color: #64748b;
  margin: 0;
}

/* 聊天内容区域 */
.conversation-area {
  flex: 1;
  overflow-y: auto;
  padding-right: 8px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

/* 消息外层布局 */
.message-wrapper {
  display: flex;
  gap: 12px;
  align-items: flex-start;
  width: 100%;
}

.user-wrapper {
  flex-direction: row-reverse;
}

/* 头像 */
.avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  flex-shrink: 0;
  border: 1px solid #e2e8f0;
  background-color: #f8fafc;
}

.user-avatar {
  background-color: var(--theme-color-resolved);
  border-color: var(--theme-color-resolved);
  color: #ffffff;
}

.assistant-avatar {
  background-color: #f8fafc;
}

/* 消息气泡容器 */
.message-bubble-container {
  display: flex;
  flex-direction: column;
  max-width: 90%;
}

.user-wrapper .message-bubble-container {
  align-items: flex-end;
}

/* 气泡本体 */
.message-bubble {
  padding: 10px 16px;
  border-radius: 18px;
  font-size: 14.5px;
  line-height: 1.6;
  position: relative;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.user-bubble {
  background-color: var(--theme-color-resolved);
  color: #ffffff;
  border-bottom-right-radius: 4px;
}

.user-bubble p {
  margin: 0;
}

.assistant-bubble {
  background-color: #f1f5f9;
  border: 1px solid #e2e8f0;
  color: #1e293b;
  border-bottom-left-radius: 4px;
}

.assistant-bubble p {
  margin: 0;
  word-break: break-word;
}

/* 声明信息 */
.ai-disclaimer {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: 11px;
  color: #64748b;
  margin-top: 6px;
  margin-left: 4px;
  font-style: normal;
}

.info-icon {
  width: 12px;
  height: 12px;
  opacity: 0.8;
}

/* ChatGPT 思考中状态 */
.thinking-process {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
  font-size: 13px;
  color: var(--theme-color-resolved);
  font-weight: 500;
}

.thinking-spinner {
  width: 14px;
  height: 14px;
  border: 2px solid #e2e8f0;
  border-top-color: var(--theme-color-resolved);
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.typing-indicator {
  display: flex;
  gap: 5px;
  padding: 4px 0;
  align-items: center;
}

.typing-indicator span {
  width: 8px;
  height: 8px;
  background-color: var(--theme-color-resolved);
  border-radius: 50%;
  opacity: 0.4;
  animation: bounce 1.4s infinite ease-in-out both;
}

.typing-indicator span:nth-child(1) {
  animation-delay: -0.32s;
}

.typing-indicator span:nth-child(2) {
  animation-delay: -0.16s;
}

@keyframes bounce {
  0%, 80%, 100% {
    transform: scale(0.6);
    opacity: 0.4;
  }
  40% {
    transform: scale(1.2);
    opacity: 1;
  }
}

/* 输入框区域 */
.input-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
  width: 100%;
}

.input-wrapper {
  display: flex;
  align-items: center;
  gap: 12px;
  background-color: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 24px;
  padding: 4px 6px 4px 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.03);
}

.input-wrapper:focus-within {
  border-color: var(--theme-color-resolved);
  background-color: #ffffff;
}

.input-wrapper input {
  flex-grow: 1;
  border: none;
  background: transparent;
  outline: none;
  color: #1e293b;
  font-size: 14.5px;
  padding: 10px 0;
  line-height: 1.4;
}

.input-wrapper input::placeholder {
  color: #64748b;
  opacity: 0.8;
}

.input-wrapper input:disabled {
  cursor: not-allowed;
}

/* 发送按钮 */
.send-button {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background-color: var(--theme-color-resolved);
  color: #ffffff;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.send-button:disabled {
  background-color: #e2e8f0;
  color: #64748b;
  cursor: not-allowed;
  opacity: 0.5;
}

.send-icon {
  width: 15px;
  height: 15px;
  transform: translate(1px, -1px);
}

.send-button:disabled .send-icon {
  transform: none;
}

/* 页脚免责 */
.input-footer {
  font-size: 11.5px;
  color: #64748b;
  text-align: center;
  opacity: 0.85;
}

/* ==================== Markdown 样式渲染 ==================== */
.markdown-content {
  word-break: break-word;
}

.markdown-content :deep(p) {
  margin: 0 0 3px 0;
}

.markdown-content :deep(p:last-child) {
  margin-bottom: 0;
}

.markdown-content :deep(strong) {
  font-weight: 700;
  color: inherit;
}

.markdown-content :deep(em) {
  font-style: italic;
}

.markdown-content :deep(a.markdown-link) {
  color: var(--theme-color-resolved);
  text-decoration: none;
  font-weight: 500;
}

.markdown-content :deep(a.markdown-link:hover) {
  text-decoration: underline;
}

/* 标题样式 */
.markdown-content :deep(h1),
.markdown-content :deep(h2),
.markdown-content :deep(h3),
.markdown-content :deep(h4),
.markdown-content :deep(h5),
.markdown-content :deep(h6) {
  margin: 10px 0 4px 0;
  font-weight: 600;
  line-height: 1.3;
  color: #1e293b;
}

.markdown-content :deep(h1) { font-size: 1.3em; }
.markdown-content :deep(h2) { font-size: 1.2em; }
.markdown-content :deep(h3) { font-size: 1.1em; }
.markdown-content :deep(h4) { font-size: 1.0em; }

/* 列表样式 */
.markdown-content :deep(ul),
.markdown-content :deep(ol) {
  margin: 0 0 4px 0;
  padding-left: 20px;
}

.markdown-content :deep(li) {
  margin-bottom: 2px;
}

/* 行内代码与代码块 */
.markdown-content :deep(.inline-code) {
  font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
  background-color: rgba(62, 175, 124, 0.1);
  color: var(--theme-color-resolved);
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 0.9em;
}

.markdown-content :deep(pre.code-block) {
  font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
  background-color: #1e1e24;
  color: #f8f8f2;
  padding: 12px 16px;
  border-radius: 8px;
  overflow-x: auto;
  margin: 12px 0;
  font-size: 0.85em;
  line-height: 1.5;
}

.markdown-content :deep(pre.code-block code) {
  font-family: inherit;
  color: inherit;
  background: none;
  padding: 0;
  border-radius: 0;
}

.markdown-spacing {
  height: 6px;
}

/* 用户气泡内特殊样式适配 */
.user-bubble :deep(.inline-code) {
  background-color: rgba(255, 255, 255, 0.2) !important;
  color: #ffffff !important;
}

.user-bubble :deep(a.markdown-link) {
  color: #ffffff !important;
  text-decoration: underline;
}

.markdown-content pre.code-block {
  font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
  background-color: #1e1e24;
  color: #f8f8f2;
  padding: 12px 16px;
  border-radius: 8px;
  overflow-x: auto;
  margin: 12px 0;
  font-size: 0.85em;
  line-height: 1.5;
}

.markdown-content pre.code-block code {
  font-family: inherit;
  color: inherit;
  background: none;
  padding: 0;
  border-radius: 0;
}

.markdown-spacing {
  height: 6px;
}

/* 用户气泡内特殊样式适配 */
.user-bubble .inline-code {
  background-color: rgba(255, 255, 255, 0.2) !important;
  color: #ffffff !important;
}

.user-bubble a.markdown-link {
  color: #ffffff !important;
  text-decoration: underline;
}


/* ==================== 显式深色模式 (Explicit Dark Mode) ==================== */
/* 适配 VuePress html.dark, html[data-theme="dark"], 以及 .dark 命名空间，完美覆盖所有深色切换机制 */
html.dark .ai-assistant-container,
html[data-theme="dark"] .ai-assistant-container,
.dark .ai-assistant-container {
  background-color: #1a1a1e !important;
  border-color: #2e2e34 !important;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3) !important;
}

html.dark .assistant-header,
html[data-theme="dark"] .assistant-header,
.dark .assistant-header {
  border-bottom-color: #2e2e34 !important;
}

html.dark .header-icon,
html[data-theme="dark"] .header-icon,
.dark .header-icon {
  background-color: #232329 !important;
  border-color: #2e2e34 !important;
}

html.dark .assistant-title,
html[data-theme="dark"] .assistant-title,
.dark .assistant-title {
  color: #f1f5f9 !important;
}

html.dark .assistant-subtitle,
html[data-theme="dark"] .assistant-subtitle,
.dark .assistant-subtitle {
  color: #94a3b8 !important;
}

html.dark .avatar,
html[data-theme="dark"] .avatar,
.dark .avatar {
  border-color: #2e2e34 !important;
  background-color: #232329 !important;
}

html.dark .user-avatar,
html[data-theme="dark"] .user-avatar,
.dark .user-avatar {
  background-color: var(--theme-color-resolved) !important;
  border-color: var(--theme-color-resolved) !important;
  color: #ffffff !important;
}

html.dark .assistant-bubble,
html[data-theme="dark"] .assistant-bubble,
.dark .assistant-bubble {
  background-color: #232329 !important;
  border-color: #34343d !important;
  color: #e2e8f0 !important;
}

html.dark .ai-disclaimer,
html[data-theme="dark"] .ai-disclaimer,
.dark .ai-disclaimer {
  color: #94a3b8 !important;
}

html.dark .input-wrapper,
html[data-theme="dark"] .input-wrapper,
.dark .input-wrapper {
  background-color: #232329 !important;
  border-color: #2e2e34 !important;
}

html.dark .input-wrapper:focus-within,
html[data-theme="dark"] .input-wrapper:focus-within,
.dark .input-wrapper:focus-within {
  border-color: var(--theme-color-resolved) !important;
  background-color: #1a1a1e !important;
}

html.dark .input-wrapper input,
html[data-theme="dark"] .input-wrapper input,
.dark .input-wrapper input {
  color: #f1f5f9 !important;
}

html.dark .input-wrapper input::placeholder,
html[data-theme="dark"] .input-wrapper input::placeholder,
.dark .input-wrapper input::placeholder {
  color: #64748b !important;
}

html.dark .send-button:disabled,
html[data-theme="dark"] .send-button:disabled,
.dark .send-button:disabled {
  background-color: #2e2e34 !important;
  color: #64748b !important;
}

html.dark .input-footer,
html[data-theme="dark"] .input-footer,
.dark .input-footer {
  color: #64748b !important;
}

html.dark .markdown-content :deep(h1),
html.dark .markdown-content :deep(h2),
html.dark .markdown-content :deep(h3),
html.dark .markdown-content :deep(h4),
html[data-theme="dark"] .markdown-content :deep(h1),
html[data-theme="dark"] .markdown-content :deep(h2),
html[data-theme="dark"] .markdown-content :deep(h3),
html[data-theme="dark"] .markdown-content :deep(h4),
.dark .markdown-content :deep(h1),
.dark .markdown-content :deep(h2),
.dark .markdown-content :deep(h3),
.dark .markdown-content :deep(h4) {
  color: #f1f5f9 !important;
}

html.dark .markdown-content :deep(.inline-code),
html[data-theme="dark"] .markdown-content :deep(.inline-code),
.dark .markdown-content :deep(.inline-code) {
  background-color: rgba(62, 175, 124, 0.15) !important;
  color: #52d395 !important;
}

/* 响应式超级优化 (Mobile Premium Optimizations) */
@media (max-width: 768px) {
  .ai-assistant-container {
    padding: 12px 4px !important;
    margin: 10px 0 !important;
    border: none !important;
    box-shadow: none !important;
    background-color: transparent !important;
    height: 100% !important;
  }

  /* 移动端深色模式下同样去除多重边框与多重背景，使其与整体页面底色完美融合 */
  html.dark .ai-assistant-container,
  html[data-theme="dark"] .ai-assistant-container,
  .dark .ai-assistant-container {
    background-color: transparent !important;
    border: none !important;
    box-shadow: none !important;
  }
  
  .message-bubble-container {
    max-width: 88% !important;
  }
  
  .avatar {
    width: 32px;
    height: 32px;
    font-size: 15px;
  }
  
  .conversation-area {
    padding-right: 0 !important;
    gap: 16px !important;
    min-height: 200px !important;
  }
  
  .message-bubble {
    padding: 10px 14px !important;
    font-size: 14px !important;
    border-radius: 16px !important;
  }

  .user-bubble {
    border-bottom-right-radius: 4px !important;
  }

  .assistant-bubble {
    border-bottom-left-radius: 4px !important;
  }

  .assistant-header {
    padding-bottom: 12px !important;
    margin-bottom: 4px !important;
  }

  .assistant-title {
    font-size: 18px !important;
  }

  .assistant-subtitle {
    font-size: 12px !important;
  }
}
</style>
