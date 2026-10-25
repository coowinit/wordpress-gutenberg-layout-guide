# WordPress Gutenberg 页面布局与组件复用规范

> 本文整理 Gutenberg 布局、区块结构、Pattern 复用、CSS 维护，以及从 HTML
> 原型转换为可编辑 Gutenberg 页面的方法。适用于使用 WordPress Gutenberg
> 与经典主题制作企业官网和产品详情页。

## 1. 核心原理

Gutenberg 页面由三个部分协作完成：

1.  **Block 结构**：决定内容由哪些区块组成，以及区块之间的层级。
2.  **HTML 输出**：WordPress 将区块结构输出为前端 HTML。
3.  **CSS 样式**：控制颜色、字体、间距、边框、布局和响应式效果。

推荐分工：

-   Gutenberg：管理标题、文字、图片、表格、按钮和基本布局。
-   主题 CSS：维护组件与全站视觉样式。
-   PHP 模板：控制经典主题的页面框架、动态数据和程序逻辑。
-   Synced Pattern：复用需要统一更新的固定内容。

**原则：先设计结构，再添加样式；优先组合原生区块，只有确有需要时才开发自定义区块或插件。**

## 2. Group、Row、Stack、Columns 与 Grid

### Group：组织模块

将多个区块放进同一个容器，便于统一管理背景、内边距、边框等样式。适合独立内容区、产品详情模块、标题加说明等。Group
本身不一定是多列布局。

### Row：横向排列

适合一行按钮、图标与文字、简短信息项等。

### Stack：纵向排列

适合标题、描述、按钮等从上到下排列的内容。

### Columns：明确分栏

将内容划分为明确的列，每个 Column 可以包含多个
Block。适合图文并排、左侧文案右侧卡片、需要控制列宽比例的布局。

### Grid：规则网格

适合多个同类项目按行列排列，例如产品特点、企业优势卡片、图标功能列表。

  需求                         优先考虑
  ---------------------------- ----------
  组织独立模块、设置整体背景   Group
  横向排列                     Row
  纵向排列                     Stack
  明确分成左右或多列           Columns
  多张同类卡片规则排列         Grid

这些是推荐规则，不是强制限制。简单布局不必为了层级完整而额外增加容器。不同
WordPress 版本提供的布局选项可能不同。

## 3. 推荐的区块层级

### 图文模块

``` text
Group（整体模块）
└── Columns（左右布局）
    ├── Column（文字）
    │   ├── Heading
    │   ├── Paragraph
    │   └── Buttons
    └── Column（图片）
        └── Image
```

### 重复卡片

``` text
Group（优势模块）
├── Heading（模块标题）
└── Grid（卡片布局）
    ├── 卡片 1
    ├── 卡片 2
    ├── 卡片 3
    └── 卡片 4
```

### 产品 Customization 模块

``` text
Group（可选的整体容器）
└── Columns（左右布局）
    ├── Column（左侧文案）
    │   ├── Heading
    │   ├── Paragraph
    │   └── Buttons
    └── Column（右侧卡片）
        └── Columns（双卡片）
            ├── Column（Colors）
            └── Column（Materials）
```

如果当前结构显示正常、CSS 也没有冲突，不必仅仅为了增加 Group 而重做。

## 4. List View（列表视图）

List View
用于查看页面的区块层级，适合快速选择外层容器、找到嵌套较深的区块、移动或复制区块，以及检查是否存在不必要的嵌套。复杂页面维护时，优先通过
List View 选择目标区块。

## 5. Copy、Pattern 与 Synced Pattern

### Copy / Paste

复制区块后粘贴到另一页面，可以快速复用内容结构。如果复制的是 Synced
Pattern
实例，粘贴后通常仍引用同一个同步模式，而不是独立副本。复制后可检查同步图标和模式名称确认。

### Duplicate

Duplicate
用于复制区块。普通区块通常产生独立副本；同步模式实例的行为应以当前
WordPress 版本中的实际结果为准。

### 普通 Pattern

普通 Pattern
是可重复使用的布局起点。插入后，各页面通常可以独立修改内容。

### Synced Pattern

Synced Pattern（同步模式）适合多个页面共用、并需要统一更新的固定内容。

创建流程通常为：

1.  在 List View 中选中需要复用的完整区块。
2.  打开区块工具栏的更多选项菜单。
3.  选择创建 Pattern / 模式。
4.  命名，例如 `Product Customization`。
5.  启用同步选项并保存。
6.  在其他页面通过 Patterns → My patterns 插入，或复制已有同步模式实例。

不同 WordPress 版本的菜单名称可能略有区别。

### Detach（解除关联）

Detach
会将同步模式实例转换为独立内容。解除关联后，该实例不再跟随原模式更新。只有明确希望某个页面独立维护时才使用。

### 产品详情页的推荐策略

  -----------------------------------------------------------------------------------
  内容                    推荐方式                       原因
  ----------------------- ------------------------------ ----------------------------
  产品参数表格            每个产品独立的 Table Block     不同产品参数不同

  固定 Customization 模块 Synced Pattern                 修改一次，统一更新

  动态产品数据或图库      主题模板、动态区块或数据机制   需要根据当前产品变化

  整页布局起点            普通 Pattern                   保留结构，同时允许独立修改
                          或复制现有页面结构             
  -----------------------------------------------------------------------------------

同步模式适合固定内容，不应被当作自动读取当前产品字段的动态组件。

## 6. 从 HTML 原型转换为 Gutenberg

可以先制作 HTML 页面并确认视觉设计，再转换为 Gutenberg
原生区块。这样能先验证设计，同时让后续内容在编辑器中继续维护。

推荐流程：

1.  完成 HTML 原型，确认布局、字体、颜色、间距、图片和响应式效果。
2.  设计 Block 层级，选择 Group、Columns、Grid 和具体内容 Block。
3.  生成 Gutenberg 区块标记，确保 WordPress 能识别区块类型和属性。
4.  为组件添加专属 CSS 类，例如
    `demo-card-section`、`demo-card-grid`、`demo-card`。
5.  将样式放入主题 CSS 或组件专属 CSS 文件。
6.  在测试页面的 Code editor 中导入完整区块标记，再切回 Visual editor
    检查。
7.  检查桌面端、手机端和实际前端效果。

### 两种粘贴方式

-   **普通内容粘贴**：适合文字、简单内容或 WordPress 能自动识别的内容。
-   **完整 Gutenberg
    区块标记**：适合需要精确控制区块层级、区块属性和自定义类名的场景。

将普通 HTML 粘贴到 Paragraph 中，不一定会生成正确的原生 Block 结构。完整
Gutenberg 标记也必须与区块类型和 HTML 结构匹配。

示例：

``` html
<!-- wp:group {"className":"demo-card-section","layout":{"type":"constrained"}} -->
<div class="wp-block-group demo-card-section">

    <!-- wp:heading -->
    <h2 class="wp-block-heading">Our Advantages</h2>
    <!-- /wp:heading -->

    <!-- wp:paragraph -->
    <p>Discover what makes our products different.</p>
    <!-- /wp:paragraph -->

</div>
<!-- /wp:group -->
```

`<!-- wp:group ... -->` 是 Gutenberg
用于识别区块的标记；`demo-card-section` 是自定义 CSS 类；HTML
元素是区块输出结构。以上是简化示例，正式使用时应保证区块注释、HTML
和区块属性匹配。

## 7. CSS 与主题维护规范

### 使用专属类名

例如：

-   `product-customization`
-   `demo-card-section`
-   `demo-card-grid`
-   `demo-card`

通常可在区块的 Advanced（高级）设置中填写 Additional CSS class(es)。

### 样式职责

  样式或逻辑                         推荐位置
  ---------------------------------- -------------------------
  标题、文字、图片、表格内容         Gutenberg 编辑器
  组件背景、边框、间距、卡片样式     主题 CSS 或组件专属 CSS
  全站字体、颜色、通用按钮           主题公共 CSS
  产品图库、相关产品、动态模板结构   主题 PHP 模板及相关脚本
  多页面共享的固定内容               Synced Pattern

### 避免全局样式污染

优先使用组件专属选择器：

``` css
.product-customization .customization-card {
    border: 1px solid #ded6cc;
    border-radius: 6px;
}
```

不要为了修改一个模块而随意全局覆盖 `.wp-block-columns` 或
`.wp-block-group`，否则可能影响其他页面。

主题前端 CSS 不一定会完整反映在 Gutenberg
编辑器画布中，因此应同时检查编辑器和实际前端页面。

## 8. 响应式布局检查

每个布局完成后，至少检查：

-   Columns 在手机端是否按预期堆叠。
-   Grid 是否在窄屏下减少列数。
-   Gap、Padding、Margin 是否合适。
-   图片是否变形、裁切或溢出。
-   标题与段落是否出现不合理换行。
-   页面是否出现横向滚动条。
-   按钮在手机端是否容易点击。
-   卡片高度和对齐方式是否合理。

不要假定所有主题和区块的默认表现完全相同，应以实际主题、WordPress
版本和前端测试为准。

## 9. Elementor 与 Gutenberg 概念对照

  ------------------------------------------------------------------------------
  Elementor 概念          Gutenberg               说明
                          中可参考的概念          
  ----------------------- ----------------------- ------------------------------
  Container               Group、Row、Stack 等    概念相近，但设置并非完全相同

  Flexbox Container       Row、Stack、Group       具体能力取决于编辑器版本
                          的布局选项              

  Grid Container          Grid 布局               适合规则网格

  内部列布局              Columns / Column        适合明确分栏

  Navigator / Structure   List View               查看和管理区块层级

  可复用模板或组件        Pattern / Synced        需要区分是否同步
                          Pattern                 
  ------------------------------------------------------------------------------

不要为了模仿 Elementor 而给每个 Block
都增加容器。应选择最简单、最容易维护的结构。

## 10. 新建页面的工作流程

1.  明确页面要展示的内容和模块。
2.  先画出简单的区块树结构。
3.  选择 Group、Columns 或 Grid。
4.  添加 Heading、Paragraph、Image、Table、Buttons 等基础 Block。
5.  使用 List View 检查层级，删除不必要的嵌套。
6.  为特殊模块添加专属 CSS 类。
7.  将 CSS 放入合适的主题样式文件。
8.  为需要统一更新的固定内容创建 Synced Pattern。
9.  检查桌面端和手机端。
10. 保存并检查前端效果。

## 11. 发布前检查清单

-   [ ] 页面结构清晰，没有不必要的嵌套。
-   [ ] 标题层级合理，内容语义明确。
-   [ ] Group、Columns、Grid 的选择符合布局需求。
-   [ ] 自定义 CSS 类名清楚且无冲突。
-   [ ] CSS 只影响目标组件，没有破坏其他页面。
-   [ ] 普通 Pattern 与 Synced Pattern 的使用目的明确。
-   [ ] 需要同步的固定模块已确认同步状态。
-   [ ] 每个产品独立的参数仍可单独编辑。
-   [ ] 桌面端和手机端均已测试。
-   [ ] 已检查实际前端，不只依赖编辑器画布。
-   [ ] 交互、链接、图片和按钮均已验证。

## 12. 最终原则

-   **Group 管模块，Columns 管明确分栏，Grid 管重复内容的规则排列。**
-   **先设计结构，再写样式。**
-   **内容由 Gutenberg 管理，视觉样式由 CSS
    管理，动态逻辑由主题或程序代码负责。**
-   **固定内容使用 Synced Pattern，需要独立变化的内容单独维护。**
-   **优先使用原生 Block，只有原生能力不足时才开发自定义区块或插件。**
-   **以实际前端测试为准，不因结构更复杂就认为更专业。**


## 13. 在线查找与复制 Gutenberg Pattern

可以先在在线模式库中浏览布局，点击 **Copy / Copy Pattern**，再回到 WordPress Gutenberg 编辑器按 `Ctrl + V` 粘贴。粘贴后通过 List View 检查区块结构，并按当前主题调整样式。

### 推荐资源

- **[WordPress.org Patterns](https://wordpress.org/patterns/)**：优先使用，适合研究原生 Gutenberg 布局，例如卡片、图文、CTA 和网格。
- **[WordPress.com Pattern Library](https://wordpress.com/patterns/)**：可浏览和预览不同布局；部分功能可能要求登录。
- **[Gutenberg Hub](https://gutenberghub.com/)**：第三方布局资源，可作为补充；具体复制功能和授权以每个页面的说明为准。
- **[Ollie 开源区块主题](https://github.com/OllieWP/ollie)**：适合研究开源模式的区块标记和布局组织方式。
- **[Twenty Twenty-Five 模式源码](https://github.com/WordPress/wordpress-develop/tree/trunk/src/wp-content/themes/twentytwentyfive/patterns)**：学习 WordPress 官方主题如何组合原生区块。

### 复制与使用注意事项

1. **复制不等于自动保存为 Pattern。** 粘贴后通常只是当前页面中的区块；需要重复使用时，再保存为普通 Pattern 或 Synced Pattern。
2. **检查区块结构。** 使用 List View 确认 Group、Columns、Grid 及内容区块的层级是否清晰。
3. **检查主题样式。** 复制的布局不一定会自动带来原网站的字体、颜色、间距或额外 CSS；必要时为组件添加专属类名，并在主题 CSS 中实现样式。
4. **注意插件依赖。** 某些模式可能使用第三方区块，若目标网站未安装对应插件，布局可能无法完整还原。优先选择原生 Gutenberg 区块。
5. **检查图片与授权。** 复制布局不代表图片已导入自己的媒体库，也不代表获得图片使用授权；正式上线前应确认来源、许可及图片地址。

### 推荐研究流程

1. 在模式库中搜索 `card`、`features`、`grid`、`image text` 或 `call to action`。
2. 预览并复制一个符合需求的布局。
3. 粘贴到测试页面，检查 List View 和前端效果。
4. 记录原始链接、区块结构、依赖项及需要补充的 CSS。
5. 测试桌面端与手机端后，再整理为 `examples/` 下的独立实例。

建议每个实例统一包含：`README.md`（说明与步骤）、`block-markup.html`（可复制的 Gutenberg 区块标记）和 `style.css`（组件专属样式）。先研究并验证少量高价值实例，再逐步扩充目录。
