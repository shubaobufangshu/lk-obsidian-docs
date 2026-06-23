# 一、Element Plus 核心基础认知

## 1.1 核心简介

Element Plus 是**饿了么官方开源、基于Vue3开发的企业级PC端UI组件库**，是旧版Element UI的Vue3升级迭代版本。专为中后台管理系统、企业办公平台、数据运营后台等PC项目打造，内置全套开箱即用的UI组件、设计规范、交互逻辑，无需手写基础样式，大幅提升前端开发效率，国内使用率最高、生态最全、社区问题解决方案最丰富。

## 1.2 核心核心优势（开发必知）

- **Vue3深度适配**：完美兼容组合式API、setup语法糖、TypeScript全类型提示，无兼容报错。
- **组件全覆盖**：涵盖布局、表单、表格、弹窗、导航、图标、上传、分页等99%后台开发刚需组件。
- **性能优化完善**：支持按需引入、自动按需导入，打包体积小，不冗余加载无用代码。
- **高度可定制化**：支持主题色修改、暗黑模式、全局样式变量自定义，适配企业定制UI风格。
- **配套齐全**：自带国际化、表单校验、数据反馈、权限适配，开箱即用无需二次改造。

## 1.3 必备前置基础（无需精通，会即可）

- Vue3 基础语法 + setup语法糖核心用法
- HTML/CSS 基础布局、Flex常用样式
- npm/pnpm 基础依赖安装命令
- Vite项目基础启动与配置

# 二、环境搭建与项目初始化（一步不差照着做）

## 2.1 环境要求

- Node.js 版本：18.0.0及以上LTS稳定版
- 构建工具：Vite（Vue3官方推荐，本教程全程使用）
- 包管理器：pnpm（推荐，速度快、占用低，npm/yarn通用）

## 2.2 创建Vue3 Vite新项目

终端执行以下命令，快速初始化干净Vue3项目：

```bash
# pnpm 创建项目
pnpm create vite@latest element-plus-demo --template vue

# 进入项目目录
cd element-plus-demo

# 安装基础依赖
pnpm install

# 启动本地开发服务
pnpm dev
```

启动成功后浏览器打开默认地址**http://localhost:5173**，看到Vue默认页面即为项目初始化完成。

# 三、Element Plus 三种引入方式（重点掌握第三种）

## 3.1 安装Element Plus核心依赖

```bash
pnpm add element-plus
```

## 3.2 方式一：完整引入（新手测试用，生产不推荐）

配置简单、一键注册所有组件，缺点打包体积大，仅适合新手练习、本地测试。

修改 **src/main.js** 配置：

```javascript
import { createApp } from 'vue'
import App from './App.vue'
// 引入Element Plus核心与样式
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

const app = createApp(App)
app.use(ElementPlus)
app.mount('#app')
```

配置完成后，所有Element Plus组件可直接在页面使用，无需单独导入。

## 3.3 方式二：自动按需引入（官方推荐·生产必备·重点掌握）

只打包页面实际用到的组件和样式，体积最小、开发高效，企业项目统一使用该方式。

### 第一步：安装自动按需导入插件

```bash
pnpm add -D unplugin-vue-components unplugin-auto-import
```

### 第二步：修改 vite.config.js 配置

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  plugins: [
    vue(),
    AutoImport({ resolvers: [ElementPlusResolver()] }),
    Components({ resolvers: [ElementPlusResolver()] })
  ]
})
```

### 第三步：重启项目生效

终止当前dev服务，重新执行 `pnpm dev`，无需在main.js引入任何Element Plus内容，直接在模板使用组件即可。

## 3.4 方式三：手动按需引入（了解即可，不推荐使用）

每个组件手动引入注册，代码冗余、开发效率低，仅老旧项目维护使用。

```vue
<template>
  <el-button type="primary">测试按钮</el-button>
</template>

<script setup>
import { ElButton } from 'element-plus'
import 'element-plus/es/components/button/style/css'
</script>
```

# 四、Element Plus 图标库单独配置与使用

## 4.1 安装图标依赖

```bash
pnpm add @element-plus/icons-vue
```

## 4.2 局部按需使用（推荐）

```vue
<template>
  <el-button type="primary">
    <el-icon><Search /></el-icon>
    搜索查询
  </el-button>
</template>

<script setup>
import { Search, Edit, Delete } from '@element-plus/icons-vue'
</script>
```

# 五、全局基础通用配置（项目统一初始化必配）

## 5.1 全局组件默认尺寸、层级配置

统一项目所有组件默认大小、弹窗层级，避免页面样式杂乱。

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import ElementPlus from 'element-plus'

const app = createApp(App)
app.use(ElementPlus, {
  size: 'default', // 全局默认尺寸 large / default / small
  zIndex: 3000 // 弹窗初始层级，防止被遮挡
})
app.mount('#app')
```

## 5.2 国际化配置（默认中文，可切换多语言）

```javascript
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'
app.use(ElementPlus, { locale: zhCn })
```

# 六、高频核心组件实操精讲（后台开发99%常用）

## 6.1 布局组件（页面骨架搭建核心）

### 6.1.1 Container 后台经典整体布局

适配所有管理系统**侧边栏+顶部导航+主内容**标准结构，直接复制即用。

```vue
<template>
  <el-container style="height: 100vh;">
    <el-aside width="220px" style="background: #f5f7fa;">侧边菜单区域</el-aside>
    <el-container>
      <el-header style="background: #fff;border-bottom:1px solid #eee;">顶部导航栏</el-header>
      <el-main style="background: #f5f5f5;">页面核心内容区</el-main>
    </el-container>
  </el-container>
</template>
```

### 6.1.2 Row+Col 24栅格布局

页面内容分栏、卡片排列专用，24格均分，灵活适配各种布局场景。

```vue
<template>
  <div style="margin:20px">
    <el-row :gutter="20">
      <el-col :span="12"><div style="height:60px;background:#409eff">左半区域</div></el-col>
      <el-col :span="12"><div style="height:60px;background:#67c23a">右半区域</div></el-col>
    </el-row>
  </div>
</template>
```

## 6.2 Button 按钮组件（基础交互核心）

涵盖类型、尺寸、状态、点击事件，开发高频使用。

```vue
<template>
  <div style="margin:20px">
    <!-- 基础类型 -->
    <el-button>默认按钮</el-button>
    <el-button type="primary">主要按钮</el-button>
    <el-button type="success">成功按钮</el-button>
    <el-button type="danger">危险按钮</el-button>

    <!-- 常用状态 -->
    <el-button type="primary" plain>朴素按钮</el-button>
    <el-button type="primary" round>圆角按钮</el-button>
    <el-button type="primary" disabled>禁用按钮</el-button>
    <el-button type="primary" loading>加载按钮</el-button>

    <!-- 点击事件 -->
    <el-button type="primary" @click="clickBtn">点击触发</el-button>
  </div>
</template>

<script setup>
import { ElMessage } from 'element-plus'
const clickBtn = () => {
  ElMessage.success('按钮点击成功')
}
</script>
```

## 6.3 Form表单组件+表单校验（后台核心重中之重）

用户新增、编辑、登录、数据提交必备，内置完整校验规则，直接复用。

```vue
<template>
  <el-form ref="formRef" :model="formData" :rules="formRules" label-width="100px" style="width:600px;margin:30px auto">
    <el-form-item label="用户名" prop="username">
      <el-input v-model="formData.username" placeholder="请输入用户名"></el-input>
    </el-form-item>
    <el-form-item label="手机号" prop="phone">
      <el-input v-model="formData.phone" placeholder="请输入手机号"></el-input>
    </el-form-item>
    <el-form-item label="登录密码" prop="password">
      <el-input v-model="formData.password" type="password" show-password placeholder="请输入密码"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm">提交表单</el-button>
      <el-button @click="resetForm">重置表单</el-button>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
// 表单实例
const formRef = ref()
// 表单绑定数据
const formData = reactive({
  username: '',
  phone: '',
  password: ''
})
// 表单校验规则
const formRules = reactive({
  username: [{ required: true, message: '用户名不能为空', trigger: 'blur' }],
  phone: [{ required: true, message: '手机号不能为空', trigger: 'blur' },{ pattern: /^1[3-9]\d{9}$/, message: '手机号格式错误', trigger: 'blur' }],
  password: [{ required: true, message: '密码不能为空', trigger: 'blur' },{ min: 6, message: '密码至少6位', trigger: 'blur' }]
})
// 提交校验
const submitForm = () => {
  formRef.value.validate(valid => {
    if(valid) ElMessage.success('表单提交校验通过')
    else ElMessage.error('请检查表单填写内容')
  })
}
// 重置表单
const resetForm = () =&gt; {
  formRef.value.resetFields()
}
&lt;/script&gt;
```

## 6.4 Table表格+分页组件（数据展示核心）

用户列表、数据查询、后台数据展示刚需，自带编辑删除、状态格式化、分页联动。

```vue
<template>
  <div style="margin:30px">
    <el-table :data="tableData" border style="width:100%">
      <el-table-column type="index" label="序号" width="80" align="center" />
      <el-table-column prop="name" label="姓名" />
      <el-table-column prop="phone" label="手机号" />
      <el-table-column prop="status" label="账号状态">
        <template #default="scope">
          <el-tag :type="scope.row.status === 1 ? 'success' : 'danger'">
            {{ scope.row.status === 1 ? '正常' : '禁用' }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" fixed="right" width="180">
        <template #default="scope">
          <el-button size="small" type="primary" @click="editRow(scope.row)">编辑</el-button>
          <el-button size="small" type="danger" @click="delRow(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      v-model:current-page="page.currentPage"
      v-model:page-size="page.pageSize"
      :total="page.total"
      layout="total,prev,pager,next,sizes"
      style="margin-top:20px;text-align:right"
    />
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
// 分页数据
const page = reactive({ currentPage: 1, pageSize: 10, total: 3 })
// 表格模拟数据
const tableData = ref([
  { id:1, name:'张三', phone:'13800138000', status:1 },
  { id:2, name:'李四', phone:'13900139000', status:1 },
  { id:3, name:'王五', phone:'13700137000', status:0 }
])
// 编辑
const editRow = (row) => ElMessage.info(`编辑用户：${row.name}`)
// 删除
const delRow = (row) => {
  ElMessageBox.confirm('确定删除该数据？','删除提示',{type:'warning'}).then(()=>{
    tableData.value = tableData.value.filter(item=>item.id !== row.id)
    ElMessage.success('删除成功')
  })
}
</script>
```

## 6.5 Dialog弹窗组件（新增编辑弹窗专用）

新增数据、编辑详情、弹窗表单通用，自带关闭拦截、底部按钮插槽。

```vue
<template>
  <div style="margin:30px">
    <el-button type="primary" @click="dialogShow = true">打开编辑弹窗</el-button>
    <el-dialog v-model="dialogShow" title="用户编辑" width="500px">
      <el-form label-width="80px">
        <el-form-item label="用户名"><el-input placeholder="请输入用户名"></el-input></el-form-item>
        <el-form-item label="手机号"><el-input placeholder="请输入手机号"></el-input></el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="dialogShow = false">取消</el-button>
        <el-button type="primary">确认保存</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
const dialogShow = ref(false)
</script>
```

# 七、主题自定义与暗黑模式快速配置

## 7.1 全局主题色一键修改

新建全局样式文件，覆盖Element Plus默认CSS变量，全站组件主题同步修改。

```css
:root {
  --el-color-primary: #1890ff; /* 主色调 */
  --el-color-success: #52c41a;/* 成功色 */
  --el-color-danger: #ff4d4f;/* 危险色 */
  --el-border-radius-base: 4px;/* 统一圆角 */
}
```

## 7.2 暗黑模式一键开启

html标签添加class即可全局暗黑，JS可动态切换。

```html
<html class="dark"></html>
```

# 八、开发常见报错快速解决方案

- **按需引入样式失效**：修改vite配置后必须重启项目，配置才能生效。
- **表单校验不触发**：el-form-item的prop必须和表单数据、校验规则key完全一致。
- **弹窗表单重置无效**：关闭弹窗调用resetFields()重置表单初始值。
- **表格层级错乱遮挡**：全局统一设置z-index初始值，表格避免嵌套transform父容器。