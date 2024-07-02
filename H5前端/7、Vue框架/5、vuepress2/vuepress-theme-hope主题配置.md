# 1、config.ts

```typescript
import { defineUserConfig } from 'vuepress'
import { hopeTheme } from 'vuepress-theme-hope'
import { redirectPlugin } from 'vuepress-plugin-redirect'
import { sidebar, navbar } from '../config'
export default defineUserConfig({
  base: '/',
  lang: 'zh-CN',
  title: 'ESBook',
  plugins: [
    redirectPlugin({
      // 配置选项
      config: {
        '/': '/es_doc/',
      },
    }),
  ],
  theme: hopeTheme({
    // 此处放置主题配置
    logo: '/assets/image/note.png',
    plugins: {
      //使用search pro 搜索插件时，默认不能支持中文分词，需要安装这个 "nodejs-jieba": "^0.1.2"
      searchPro: true,
    },
    // pure: true,
    navbar: navbar,
    // sidebar: ['/es_doc/集群常用命令.md', '/es_doc/索引相关命令.md'],
    sidebar: sidebar,
  }),
  // Enable it with pwa
  // shouldPrefetch: false,
})
```