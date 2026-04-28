# 前端代码开发详细规范

## 1. 代码风格规范

### 1.1 Vue 3 + TypeScript 规范

#### 组件结构
```vue
<template>
  <!-- 模板代码 -->
</template>

<script setup lang="ts">
// 1. 类型导入
import type { PropType } from 'vue'

// 2. Vue API 导入
import { ref, computed, onMounted } from 'vue'

// 3. 组件导入
import ChildComponent from './components/ChildComponent.vue'

// 4. 工具/类型导入
import { fetchData } from '@/utils/request'

// 5. 类型定义
interface PageDataItem {
  id: string
  title: string
  visible: boolean
}

// 6. Props 定义
interface Props {
  title: string
}

const props = defineProps<Props>()

// 7. Emits 定义
const emit = defineEmits<{
  (e: 'update', id: string): void
  (e: 'submit', data: PageDataItem): void
}>()

// 8. 响应式数据
const loading = ref(false)
const dataList = ref<PageDataItem[]>([])

// 9. 计算属性
const filteredList = computed(() => {
  return dataList.value.filter(item => item.visible)
})

// 10. 方法定义
const handleClick = (item: PageDataItem) => {
  emit('update', item.id)
}

// 11. 生命周期
const filteredList = computed(() => {
  return dataList.value.filter(item => item.visible)
})

// 9. 方法定义
const handleClick = (item: PageDataItem) => {
  emit('update', item.id)
}

// 10. 生命周期
onMounted(() => {
  loadData()
})

const loadData = async () => {
  loading.value = true
  try {
    const res = await fetchData()
    dataList.value = res.data
  } finally {
    loading.value = false
  }
}
</script>

<style scoped lang="scss">
/* SCSS 样式 */
</style>
```

#### 命名规范
| 类型 | 规范 | 示例 |
|------|------|------|
| 组件名 | PascalCase | `UserProfile.vue` |
| 变量/函数 | camelCase | `userName`, `getUserInfo()` |
| 常量 | SCREAMING_SNAKE_CASE | `API_BASE_URL` |
| CSS Class | kebab-case | `user-card`, `btn-primary` |
| 接口/类型 | PascalCase + 后缀 | `UserInfo`, `UserConfigType` |
| 文件名 | camelCase | `userService.ts` |

### 1.2 SCSS 规范

#### 文件组织
```scss
// 全局样式（global.scss）— 统一放 src/styles/global.scss

// 响应式混合宏（可直接在 global.scss 或组件 scss 中使用）
@mixin respond-to($breakpoint) {
  @if $breakpoint == 'mobile' {
    @media (max-width: 768px) { @content; }
  }
  @if $breakpoint == 'tablet' {
    @media (max-width: 1024px) { @content; }
  }
}

// 组件样式（component.scss）
.user-card {
  // 1. 布局属性
  display: flex;
  flex-direction: column;
  
  // 2. 盒模型属性
  padding: 16px;
  margin-bottom: 12px;
  border-radius: $border-radius;
  
  // 3. 视觉属性
  background-color: #ffffff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  
  // 4. 其他属性
  transition: all 0.3s ease;
  
  // 嵌套选择器
  &:hover {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  // 子元素
  .card-title {
    font-size: 16px;
    font-weight: 600;
    color: $text-color;
    margin-bottom: 8px;
  }
  
  // 响应式
  @include respond-to('mobile') {
    padding: 12px;
  }
}
```

#### BEM 命名规范
```scss
// Block - 块
.hero-section { }

// Element - 元素
.hero-section__title { }
.hero-section__subtitle { }

// Modifier - 修饰符
.hero-section--dark { }
.btn--primary { }
.btn--large { }
```

## 2. 文件组织详细规范

### 2.1 完整目录结构

```
my-project/
├── public/                          # 静态资源（不经过构建）
│   ├── images/                      # 图片资源
│   │   └── {module}/               # 按功能模块分文件夹（与 views/{module} 命名一致）★
│   │       └── {descriptive-name}.{ext}
│   └── data/                        # ★ 页面数据源（统一放 public 下）★
│       └── {module}/               # 与 views/{module} 命名一致
│           └── index.json          # 页面配置数据
│
├── components/                     # ★ 全局公共组件（与 src 同级，不在 src 下）★
│   └── {ComponentName}/            # 组件文件夹形式组织
│       ├── index.vue
│       └── index.scss
│
├── src/
│   ├── views/                      # 页面视图
│   │   └── {module}/              # 按功能模块创建文件夹
│   │       ├── index.vue          # 纯 DOM 结构 + 数据绑定
│   │       ├── index.scss         # 样式层
│   │       └── components/        # 页面级组件
│   │           └── {ComponentName}/
│   │               ├── index.vue
│   │               └── index.scss
│   │
│   ├── composables/               # 组合式函数
│   │   ├── useFetch.ts
│   │   └── usePagination.ts
│   │
│   ├── styles/                    # 全局样式
│   │   └── global.scss            # 只保留全局样式
│   │
│   ├── utils/                     # 工具函数
│   │   ├── index.ts
│   │   ├── request.ts             # 请求封装
│   │   └── helpers.ts
│   │
│   ├── App.vue
│   ├── main.ts
│   └── router/
│       └── index.ts
│
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

**★ 关键规则说明：**
- `public/images/{module}/` 和 `public/data/{module}/` 的 **`{module}` 命名必须与 `src/views/{module}/` 一一对应**
- `components/` 放在项目根目录，**与 `src/` 同级**
- 页面数据统一放在 `public/data/{module}/index.json`，不在 `src/` 下

### 2.2 文件存放规则详解

#### 图片资源规则
```
public/images/
├── {module-name}/          # 按功能模块分文件夹
│   └── {descriptive-name}.{ext}
└── shared/                 # 通用图片
    └── {descriptive-name}.{ext}

示例：
public/images/
├── home/
│   ├── hero-banner.jpg
│   ├── feature-1.png
│   └── feature-2.png
├── cases/
│   ├── case-mobile-mall.png
│   └── case-big-screen.png
└── shared/
    ├── logo.png
    └── icons/
        ├── arrow-right.svg
        └── search.svg
```

#### 样式文件规则

所有组件和页面的样式必须**单独抽出**，分散到各组件目录中管理。每个页面必须实现**数据与 DOM 分离**，数据源统一放 `public/data/` 下。

**页面结构（三层分离）：**
```
public/data/{module}/               # ★ 数据层：统一放 public 下 ★
└── index.json                      # 页面配置数据源（文案、标题、列表内容等）

src/views/{module}/                 # 视图层 + 样式层
├── index.vue                       # 纯 DOM 结构 + 数据绑定（不硬编码业务数据）
├── index.scss                      # 样式层
└── components/                     # 页面级子组件
    └── {ComponentName}/
        ├── index.vue
        └── index.scss

// index.vue 中引入样式和数据
<script setup lang="ts">
// ★ 数据从 public/data 导入 ★
import pageData from '../../../public/data/{module}/index.json'
</script>

<template>
  <!-- ... -->
</template>

<style scoped lang="scss">
@import './index.scss';
</style>
```

**全局组件样式结构：**
```
components/                          # ★ 与 src 同级 ★
└── {ComponentName}/
    ├── index.vue
    └── index.scss
```

## 3. Figma 设计稿还原规范

### 3.1 开发流程

```
用户输入设计稿 → AI 内部解析 → 提取设计 Token → 生成数据源 JSON → 编写页面代码 → 交付完整文件
```

**详细步骤：**
1. **内部解析设计稿** — 识别布局结构、组件拆分边界、交互区域
2. **提取设计 Token** — 颜色、字体、间距、圆角、阴影等视觉参数
3. **生成数据源文件** — 将文案、列表数据等业务信息抽离到 JSON
4. **编写页面代码** — 按规范目录结构输出 Vue + SCSS 文件
5. **响应式适配** — 根据设计稿类型完成适配

### 3.2 背景实现规范

#### ✅ 正确做法
```vue
<template>
  <section class="hero-section">
    <div class="hero-content">
      <h1>{{ title }}</h1>
    </div>
  </section>
</template>

<style scoped lang="scss">
.hero-section {
  // 使用 background 实现背景
  background-image: url('/images/home/hero-bg.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  min-height: 600px;
}
</style>
```

#### ❌ 错误做法
```vue
<template>
  <section class="hero-section">
    <!-- 不要用 img 做背景 -->
    <img src="/images/home/hero-bg.jpg" class="bg-image" />
    <div class="hero-content">
      <h1>{{ title }}</h1>
    </div>
  </section>
</template>
```

### 3.3 响应式布局规范

#### 响应式策略

根据**设计稿类型**决定响应式策略：

| 设计稿类型 | 响应式要求 | 实现方式 |
|-----------|-----------|---------|
| **PC 设计稿** | **必须响应式** | 使用 Media Query 向下适配移动端 |
| **移动端设计稿** | **可选** | 默认不强制适配 PC，如需适配需明确说明 |
| **双端设计稿** | **必须响应式** | 按设计稿断点完整实现 |

**开发前确认清单：**
- [ ] 确认设计稿类型（PC / 移动端 / 双端）
- [ ] PC 设计稿：默认采用响应式，适配到移动端
- [ ] 移动端设计稿：询问用户是否需要适配 PC 端
- [ ] 记录响应式需求，避免遗漏

#### 断点定义
```scss
// 响应式断点 — 可在组件 scss 中直接使用
@mixin respond-to($breakpoint) {
  $breakpoints: (
    'xs': 480px,
    'sm': 768px,
    'md': 1024px,
    'lg': 1280px,
    'xl': 1440px
  );
  $value: map-get($breakpoints, $breakpoint);
  @if $value {
    @media (max-width: $value) { @content; }
  }
}
```

#### 使用示例
```scss
.card-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 24px;
  
  @include respond-to('lg') {
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
  }
  
  @include respond-to('md') {
    grid-template-columns: repeat(2, 1fr);
    gap: 16px;
  }
  
  @include respond-to('sm') {
    grid-template-columns: 1fr;
    gap: 12px;
  }
}
```

## 4. 组件可配置性规范

### 核心原则：数据与 DOM 分离

所有页面和组件必须实现**数据与 DOM 结构分离**：
- 页面/组件的 **Vue 文件只负责 DOM 结构和数据绑定**
- 所有**业务数据（标题、文案、列表内容等）抽离到 `public/data/{module}/index.json`**
- 通过 `import` 引入 JSON 数据，通过 props 传递给子组件

### 4.1 Props 设计原则

```vue
<script setup lang="ts">
interface Props {
  // 1. 必填项
  title: string
  
  // 2. 可选项（带默认值）
  size?: 'small' | 'medium' | 'large'
  disabled?: boolean
  
  // 3. 复杂配置对象
  config?: {
    theme: 'light' | 'dark'
    showBorder: boolean
    maxWidth?: number
  }
}

const props = withDefaults(defineProps<Props>(), {
  size: 'medium',
  disabled: false,
  config: () => ({
    theme: 'light',
    showBorder: true
  })
})
</script>
```

### 4.2 页面数据分离示例

```
public/data/{module}/                # ★ 数据层：统一放 public 下 ★
└── index.json                       # 数据源

src/views/{module}/                  # 视图层 + 样式层
├── index.vue                        # 纯 DOM + 数据绑定
├── index.scss                       # 样式层
└── components/
    └── HeroSection/
        ├── index.vue
        └── index.scss
```

**public/data/{module}/index.json — 数据源：**
```json
{
  "hero": {
    "title": "欢迎使用我们的产品",
    "subtitle": "高效、灵活、易用的解决方案",
    "theme": "light",
    "backgroundImage": "/images/{module}/hero-banner.jpg"
  },
  "features": [
    { "id": 1, "title": "高性能", "desc": "毫秒级响应速度" },
    { "id": 2, "title": "易扩展", "desc": "模块化架构设计" },
    { "id": 3, "title": "安全可靠", "desc": "企业级安全保障" }
  ]
}
```

**index.vue — 纯 DOM + 数据绑定（不硬编码任何业务数据）：**
```vue
<template>
  <div class="{module}-page">
    <HeroSection :config="pageData.hero" />
    <FeatureList :items="pageData.features" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import HeroSection from './components/HeroSection/index.vue'
import FeatureList from './components/FeatureList/index.vue'
// ★ 数据从 public/data 的 JSON 导入，不硬编码 ★
import pageDataJson from '../../../public/data/{module}/index.json'

const pageData = ref(pageDataJson)
</script>

<style scoped lang="scss">
@import './index.scss';
</style>
```

### 4.3 组件示例

组件采用**文件夹形式组织**，样式单独抽出，通过 props 接收数据。

```
src/views/home/components/HeroSection/
├── index.vue
└── index.scss
```

```vue
<!-- index.vue -->
<template>
  <section 
    class="hero-section" 
    :class="[`hero-section--${config.theme}`]"
    :style="sectionStyle"
  >
    <div class="hero-section__container">
      <h1 class="hero-section__title">{{ config.title }}</h1>
      <p v-if="config.subtitle" class="hero-section__subtitle">
        {{ config.subtitle }}
      </p>
      <slot name="extra" />
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  config: {
    title: string
    subtitle?: string
    theme?: 'light' | 'dark'
    backgroundImage?: string
    maxWidth?: number
    paddingY?: number
  }
}

const props = defineProps<Props>()

const sectionStyle = computed(() => ({
  backgroundImage: props.config.backgroundImage 
    ? `url(${props.config.backgroundImage})` 
    : undefined,
  '--max-width': props.config.maxWidth 
    ? `${props.config.maxWidth}px` 
    : '1200px',
  '--padding-y': props.config.paddingY 
    ? `${props.config.paddingY}px` 
    : '80px'
}))
</script>

<style scoped lang="scss">
@import './index.scss';
</style>
```

```scss
// index.scss - 组件样式单独抽出
.hero-section {
  background-size: cover;
  background-position: center;
  padding: var(--padding-y) 20px;
  
  &__container {
    max-width: var(--max-width);
    margin: 0 auto;
  }
  
  &__title {
    font-size: 48px;
    font-weight: 600;
    margin-bottom: 16px;
  }
  
  &__subtitle {
    font-size: 18px;
    opacity: 0.8;
  }
  
  &--dark {
    color: #ffffff;
    background-color: #1a1a1a;
  }
  
  &--light {
    color: #333333;
    background-color: #ffffff;
  }
}
```
