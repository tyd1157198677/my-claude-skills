# My Claude Skills

 Claude Code 技能集合 - 前端开发系列

## 技能列表

### 1. Vue 前端开发 (vue-skill)
- **描述**: 专业的 Vue.js 前端开发助手
- **触发**: Vue、Vue 3、Composition API、Pinia、Nuxt.js
- **目录**: `vue/`

### 2. React 前端开发 (react-skill)
- **描述**: 专业的 React 前端开发助手
- **触发**: React、Hooks、JSX、Next.js、Redux
- **目录**: `react/`

### 3. TypeScript 助手 (typescript-helper)
- **描述**: 专业的 TypeScript 开发助手
- **触发**: TypeScript、类型定义、泛型、工具类型
- **目录**: `typescript/`

### 4. CSS 样式专家 (css-stylist)
- **描述**: 专业的 CSS/样式开发助手
- **触发**: CSS、Tailwind、Sass、布局、动画
- **目录**: `css/`

## 安装方法

### 在 Claude Code 中使用
1. 确保 Claude Code 已安装
2. 在设置中添加技能目录路径
3. 或在项目中创建 `.claude/settings.json` 配置技能路径

### 技能配置
```json
{
  "skills": {
    "enabled": [
      "vue-skill",
      "react-skill",
      "typescript-helper",
      "css-stylist"
    ]
  }
}
```

## 目录结构
```
my-claude-skills/
├── README.md
├── skills.json
├── vue/
│   ├── package.json
│   ├── system-prompt.md
│   ├── skill.md
│   └── examples.md
├── react/
│   ├── package.json
│   └── system-prompt.md
├── typescript/
│   ├── package.json
│   └── system-prompt.md
└── css/
    ├── package.json
    └── system-prompt.md
```

## 创建新技能

1. 创建技能目录
2. 添加 `package.json` 定义技能元数据
3. 添加 `system-prompt.md` 定义技能行为
4. （可选）添加 `skill.md` 和 `README.md` 提供文档

## License
MIT
