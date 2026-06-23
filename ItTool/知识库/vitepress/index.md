---
layout: 主页
data: 2026-05-16T08:00:00
banner_y: 0.0%
title: 主页
banner: "[[lingkong.png]]"
---

## 适配 VitePress 说明

1. 代码块使用 VitePress 支持的语言标识（`vue`/`js`/`bash`/`css`），确保语法高亮；
2. 目录使用 VitePress 兼容的 markdown 链接语法，可配合侧边栏配置实现导航；
3. 环境变量部分适配 Vite 规范（`import.meta.env`），符合 VitePress 构建逻辑；
4. 所有示例代码可直接在 Vue + Vite 项目中运行，与 VitePress 技术栈一致。

### VitePress 侧边栏配置参考（.vitepress/config.js）

```JavaScript
export default {
  themeConfig: {
    sidebar: [
      {
        text: 'Vue 核心',
        items: [
          { text: 'Vue 基础（2/3）', link: '/#vue-基础23' },
          { text: 'Ajax 网络请求', link: '/#ajax-网络请求axios' },
          { text: 'Tailwind CSS 集成', link: '/#tailwind-css-集成' },
          { text: 'Vue 组件库使用', link: '/#vue-组件库使用' }
        ]
      }
    ]
  }
}
```

## VitePress 文档适配技巧

### 1. 代码块优化

#### 1.1 行号与高亮
VitePress 支持代码块行号、高亮行、语言指定：
```typescript {1,3-5} showLineNumbers
// 高亮第1行和3-5行，显示行号
function add(a: number, b: number): number {
  const sum = a + b;
  console.log(sum);
  return sum;
}
```

#### 1.2 代码块导入文件
通过 `@[code](../path/to/file.ts)` 导入文件内容，避免复制粘贴：
```markdown
<!-- 导入 TS 示例文件 -->
@[code](@/examples/hello.ts)
```

### 2. 自定义容器增强

VitePress 内置 `tip`/`warning`/`danger`/`info` 容器，可结合 TS 示例使用：
::: info TS 最佳实践
- 避免过度使用 `any` 类型
- 优先使用接口（interface）定义对象类型
- 组合式 API 中使用 `defineProps<Props>()` 声明 Props 类型
:::

::: warning 注意事项
VitePress 中 Vue 组件的 TS 代码需放在 `<script setup lang="ts">` 中，且不支持 `export default` 形式的组件定义。
:::

### 3. 响应式示例组件

在 VitePress 中创建交互式 TS 示例组件，提升文档体验：
```vue
<!-- .vitepress/theme/components/TSDemo.vue -->
<script setup lang="ts">
import { ref } from "vue";

const inputValue = ref<string>("");
const typedValue = ref<string>("");

const handleInput = () => {
  // TS 类型校验示例
  if (inputValue.value.trim()) {
    typedValue.value = `你输入的是【字符串】：${inputValue.value}`;
  } else {
    typedValue.value = "请输入内容";
  }
};
</script>

<template>
  <div class="ts-demo">
    <input
      v-model="inputValue"
      type="text"
      placeholder="输入任意内容"
      @input="handleInput"
    />
    <p>{{ typedValue }}</p>
  </div>
</template>

<style scoped>
.ts-demo {
  padding: 16px;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  margin: 16px 0;
}
input {
  padding: 8px;
  width: 300px;
  margin-bottom: 8px;
}
</style>
```

在 Markdown 中使用：
```markdown
# TypeScript 交互示例

<TSDemo />
```
