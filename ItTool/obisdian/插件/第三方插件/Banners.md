---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

# Obsidian Banners 插件完整下载使用教程
## 一、完整安装教程
### 方法一：官方社区插件安装（推荐）
这是最稳定、自动更新的安装方式：
1. 打开 Obsidian → 左下角**设置**（齿轮图标）
2. 左侧菜单选择**第三方插件**
3. 点击**关闭安全模式**（确认弹窗）
4. 点击**浏览**按钮，进入插件市场
5. 在搜索框输入 **Banners**（作者：noatpad）
6. 点击**安装**按钮，等待安装完成
7. 安装完成后点击**启用**按钮

### 方法二：手动安装（网络不佳时使用）
如果官方市场加载失败，可以手动下载安装：

1. 访问 GitHub 仓库：https://github.com/noatpad/obsidian-banners/releases
2. 下载最新版本的 `main.js`、`manifest.json`、`styles.css` 三个文件
3. 打开你的 Obsidian 库文件夹 → `.obsidian` → `plugins`
4. 新建一个名为 `obsidian-banners` 的文件夹
5. 将下载的三个文件复制到这个文件夹中
6. 重启 Obsidian，在第三方插件列表中启用 Banners
### 设置
| 配置项 | 说明 | 推荐设置/示例 |
|--------|------|---------------|
| **Banners folder** | 定义插件默认搜索横幅图片的文件夹。留空则会搜索整个 Obsidian 库，设置固定文件夹能提升加载速度、避免图片混乱。 | 示例：`Banners`（提前在库根目录新建该文件夹，存放所有横幅图片） |
| **Enable default banners** | 开启后，所有未在 YAML 头信息指定横幅的笔记，都会显示默认横幅；关闭则只有手动指定横幅的笔记才会显示。 | ✅ 开启（你当前已开启） |
| **Default banner image** | 点击 `Select image` 可设置全局默认横幅图，作为未单独配置横幅的笔记的通用背景。 | 建议选择一张风格适配你主题的图片 |
| **Frontmatter property** | 定义 YAML 头信息中，用于指定横幅图片的属性名，默认是 `banner`，也可以改成 `cover`、`header` 等。 | 保持默认 `banner` 即可，方便后续统一维护 |
| **Default banner height** | 桌面端横幅的默认高度，支持 `px`（像素）或 `vh`（视口高度）单位。 | 你当前设置的 `200px` 是桌面端通用舒适高度，不建议超过 `300px`，否则会挤占笔记内容空间 |
| **Default mobile banner height** | 移动端（手机/平板）的横幅高度，适配小屏幕显示。 | 你当前设置的 `150px` 合理，避免移动端横幅过高遮挡内容 |
| **Banner style** | 横幅图片的视觉效果，你当前选的 `Gradient (Fade)` 是渐变透明效果，还可选：<br>- `Solid`：无渐变，纯图片显示<br>- `Dark Gradient`：深色渐变，提升文字对比度 | `Gradient (Fade)` 是最常用的，能让横幅和笔记内容过渡更自然 |
| **Content start margin** | 横幅和笔记正文之间的垂直间距，单位为像素（无需写 `px`）。填负数可以让正文文字覆盖在横幅底部，营造沉浸式效果。 | 你当前设置的 `0` 是默认值，若觉得正文和横幅贴太紧，可改为 `10`；想做文字覆盖效果可设为 `-20` |
| **Show default title** | 开启后，未单独设置标题的笔记，会在横幅上显示默认标题文本。 | ✅ 开启（你当前已开启） |
| **Show default icon** | 开启后，未单独设置图标的笔记，会在横幅上显示默认图标。 | ✅ 开启（你当前已开启） |
| **Default header text** | 横幅标题的模板文本，支持变量：<br>- `{{filename}}`：笔记文件名<br>- `{{title}}`：笔记的一级标题（`# 标题`）<br>- `{{any_frontmatter_key}}`：引用 YAML 头信息中的其他属性（如 `{{tags}}`） | 你当前设置的 `{{title}}` 是最实用的，自动显示笔记一级标题 |
| **Default icon** | 横幅默认图标，支持两种格式：<br>- Emoji 表情（如 `🌟`、`📝`）<br>- Obsidian 图标 ID（需安装 Lucide 图标主题，如 `lucide-star`） | 示例：`📝` 或 `lucide-note`，根据笔记用途选通用图标即可 |
| **Title font size** | 横幅标题的字体大小，支持 `em`/`px` 单位。 | 你当前设置的 `1.2em` 合理，和 Obsidian 正文字体比例协调 |
| **Icon size** | 横幅图标的大小，支持 `em`/`px` 单位。 | 你当前设置的 `1.5em` 合理，比标题略大，视觉上更突出 |
| **Header horizontal alignment** | 标题和图标的水平对齐方式：`Left`（左对齐）、`Center`（居中）、`Right`（右对齐）。 | 你当前设置的 `Left` 最符合阅读习惯，左对齐不会遮挡图片关键区域 |
| **Header vertical alignment** | 标题和图标的垂直对齐方式：`Top`（顶部）、`Middle`（中间）、`Bottom`（底部）。 | 你当前设置的 `Bottom` 是最优解，标题在横幅底部，不会遮挡图片主体 |
| **Header decoration** | 为横幅标题/图标添加装饰效果，提升可读性：<br>- `None`：无效果<br>- `Shadow`：文字阴影（你当前的设置，推荐，适配深色/浅色主题）<br>- `Background`：半透明背景条，适合图片对比度低的场景 | `Shadow` 是通用选择，能保证文字在任意图片上都清晰可见 |
| **Show banners in embeds** | 开启后，被嵌入到其他笔记中的子笔记（`![[笔记名]]`），会显示横幅图片。 | ✅ 开启（你当前已开启），让嵌入笔记也保持统一视觉风格 |
| **Show banners in popovers** | 开启后，鼠标悬停在笔记链接上时，弹出的预览窗口中会显示横幅。 | ✅ 开启（你当前已开启），预览时更直观 |
| **Banner height in embeds and popovers** | 嵌入笔记和弹窗预览中，横幅的默认高度。 | 你当前设置的 `120px` 合理，避免嵌入的横幅过高挤占空间；注意：笔记 YAML 中设置的 `_height` 属性优先级更高，会覆盖这个全局设置 |
| **Banners by tag** | 按标签为笔记设置专属横幅：点击 `Add new tag rule`，输入标签名（如 `#日记`），再指定对应的横幅图片。优先级从上到下，顶部规则优先生效。 | 适合分类笔记（如日记、项目、读书笔记），给不同标签的笔记设置不同风格的横幅，提升辨识度 |
| **Banner conversion tool** | 批量转换工具，点击 `Open tool` 可将笔记中旧格式的横幅属性，批量转为 Obsidian 标准的 Wiki 链接格式 `[[路径/图片.png]]`，解决格式不兼容问题。 | 适合从旧版 Banners 插件迁移的用户，一键统一格式，避免手动修改大量笔记的 YAML |

## 二、快速上手：添加你的第一个横幅
Banners 完全通过 **YAML Frontmatter** 配置，无需任何复杂语法。

### 1. 最基础用法
在笔记顶部添加以下内容：
```yaml
---
banner: "[[Banners/日记背景.png]]"  # 指定横幅图片（支持 Wiki 链接或绝对路径）
banner_icon: "📖"  # 自定义横幅图标
banner_title: "我的日记"  # 自定义横幅标题（覆盖默认标题）
banner_height: 250px  # 单条笔记横幅高度，覆盖全局设置
banner_style: "Solid"  # 单条笔记横幅样式，覆盖全局设置
banner_decoration: "Background"  # 单条笔记标题装饰效果
# 或者使用网络图片
# banner: "https://example.com/image.jpg"
---
# 笔记标题
这里是笔记正文内容
```

**效果**：图片会自动显示在笔记顶部，作为横幅背景。

### 2. 调整图片裁剪位置
默认情况下，Banners 会裁剪图片的中心部分。你可以通过 `banner_x` 和 `banner_y` 参数调整显示区域：
```yaml
---
banner: "[[风景.jpg]]"
banner_x: 0.3 # 水平位置：0=最左，1=最右，默认0.5
banner_y: 0.7 # 垂直位置：0=最上，1=最下，默认0.5
---
```

### 3. 使用 Unsplash 随机图片
Banners 内置了 Unsplash 集成，可以自动获取高质量免费图片：

1. 首先获取 Unsplash API 密钥：
   - 访问 https://unsplash.com/developers
   - 注册账号并创建一个新应用
   - 复制你的 **Access Key**
2. 打开 Banners 插件设置 → 找到 **Unsplash API Key** 输入框
3. 粘贴你的 Access Key 并保存
4. 在笔记中使用：
```yaml
---
# 随机获取与关键词匹配的图片
banner: "unsplash:mountain sunset"
banner: "unsplash:ocean beach"
banner: "unsplash:technology code"
---
```

## 三、核心配置选项详解
打开 Obsidian 设置 → 第三方插件 → Banners，你可以调整所有全局样式。

### 1. 横幅基础设置
| 设置项 | 默认值 | 说明 |
|--------|--------|------|
| Banner height | 300px | 横幅的默认高度 |
| Banner style | Gradient | 横幅样式：Solid（纯色）、Gradient（渐变）、None（无） |
| Gradient opacity | 0.7 | 渐变遮罩的不透明度 |
| Show banner in preview | 开启 | 是否在阅读模式显示横幅 |
| Show banner in source mode | 开启 | 是否在编辑模式显示横幅 |

### 2. 标题设置
| 设置项 | 默认值 | 说明 |
|--------|--------|------|
| Show note title | 开启 | 是否在横幅上显示笔记标题 |
| Title font size | 2.5rem | 标题字体大小 |
| Title alignment | Center | 标题对齐方式：Left、Center、Right |
| Title vertical alignment | Bottom | 标题垂直位置：Top、Center、Bottom |
| Show note icon | 开启 | 是否显示笔记图标（配合Iconize插件） |

### 3. 高级显示设置
- **Enable banner shadow**：开启横幅底部阴影
- **Enable banner blur**：开启横幅背景模糊效果
- **Banner blur amount**：模糊程度（默认5px）
- **Banner border radius**：横幅圆角（默认0px）
- **Hide banner in embedded notes**：在嵌入的笔记中隐藏横幅

## 四、高级用法与技巧
### 1. 给文件夹所有笔记添加默认横幅
你可以通过 `banner_folder` 参数，给整个文件夹的笔记设置相同的横幅：

1. 在文件夹的根笔记（通常是 `index.md`）中添加：
```yaml
---
banner: "[[默认横幅.jpg]]"
banner_folder: true
---
```

2. 该文件夹下所有子笔记都会自动继承这个横幅，除非子笔记自己设置了 `banner` 参数。

### 2. 添加横幅图标
配合 Obsidian Iconize 插件，可以在横幅上显示自定义图标：
```yaml
---
banner: "[[旅行.jpg]]"
banner_icon: "✈️" # 可以是emoji或Iconize图标
banner_icon_size: 3rem # 图标大小
---
```

### 3. 单笔记自定义样式
你可以在单个笔记的 Frontmatter 中覆盖全局设置：
```yaml
---
banner: "[[代码.jpg]]"
banner_height: 200px # 该笔记横幅高度为200px
banner_style: none # 该笔记不显示渐变遮罩
banner_title_alignment: left # 标题左对齐
---
```

### 4. 自定义CSS修改横幅样式
创建一个新的CSS片段，添加以下内容来实现个性化效果：
```css
/* 横幅添加边框 */
.banner {
  border-bottom: 3px solid var(--interactive-accent);
}

/* 标题添加文字阴影 */
.banner-title {
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
}

/* 调整渐变方向 */
.banner-gradient {
  background: linear-gradient(to top, rgba(0,0,0,0.8), transparent) !important;
}
```

## 五、配合MCL使用的最佳实践
Banners 与 MCL 布局完美兼容，结合使用可以达到最佳视觉效果：

1. **启用宽屏横幅**：在笔记 Frontmatter 中同时添加 `wide-page` 和 `banner`：
```yaml
---
cssClass: wide-page
banner: "[[风景.jpg]]"
---
```
横幅会自动扩展到整个页面宽度，效果更好。

2. **多列布局+横幅**：
```yaml
---
cssClass: wide-page
banner: "[[项目管理.jpg]]"
---

# 项目管理系统

> [!multi-column]
>
>> [!note] 待办事项
>> #mcl/list-card
>> - 完成需求分析
>> - 设计数据库架构
>
>> [!warning] 进行中
>> #mcl/list-card
>> - 编写技术文档
```

3. **避免标题重叠**：如果横幅高度较低，标题可能会与MCL多列布局重叠，可以适当增加横幅高度或调整标题垂直位置。

## 六、常见问题与解决方案
### 问题1：横幅不显示
**可能原因及解决方案**：
1. **图片路径错误**：确保本地图片路径正确，使用 `![[图片名]]` 格式
2. **网络图片加载失败**：检查网络连接，或换用本地图片
3. **插件未启用**：确认在第三方插件列表中启用了Banners
4. **其他CSS冲突**：暂时禁用其他CSS片段，逐一排查

### 问题2：图片拉伸变形
**解决方案**：
- 使用分辨率较高的图片（建议宽度至少1920px）
- 调整 `banner_x` 和 `banner_y` 参数，选择图片的合适区域
- 在插件设置中调整横幅高度，使其与图片比例匹配

### 问题3：标题被遮挡
**解决方案**：
- 增加 `Gradient opacity` 值，让渐变遮罩更明显
- 开启 `Enable banner blur` 选项，模糊背景
- 调整 `Title vertical alignment` 到合适位置

### 问题4：Unsplash图片加载失败
**解决方案**：
- 确认你的Unsplash API Key正确无误
- 检查网络连接是否正常
- Unsplash免费API有每日请求限制（50次/小时），超过后会暂时无法使用

