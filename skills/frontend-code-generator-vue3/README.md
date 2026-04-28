# 前端页面重构技能 (Frontend Code Generator Skill)

基于设计稿（Figma/截图）自动生成 Vue3 + TypeScript + SCSS 规范页面代码的技能。

## 功能特性
- 支持 Figma 设计稿和页面截图输入
- 输出 Vue3 + TypeScript + SCSS 规范代码
- 实现数据与 DOM 结构分离
- 响应式布局适配
- 使用 Element Plus 组件库

## 安装方式
```bash
npx skills add <owner/repo> --skill frontend-code-generator-vue3
```

## 使用说明
当用户提供设计稿（Figma 链接、截图）并要求开发前端页面时，此技能会自动触发，输出符合规范的完整页面代码。

## 技术栈
- Vue 3 (Composition API)
- TypeScript
- SCSS
- Element Plus UI 组件库

## 文件结构
```
project/
├── public/data/{module}/index.json    # 页面数据源
├── public/images/{module}/            # 图片资源
├── components/                        # 全局公共组件
└── src/views/{module}/               # 页面视图
```

## 开发规范
详细编码规范请参考 `references/coding-standards.md`。