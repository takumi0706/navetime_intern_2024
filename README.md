# How To Use

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
---

# OUTPUT
## 概要(Chapter 01)
Vue.js, HTML, CSS, JavaScriptの基礎を学ぶためのリポジトリ

### Webページの構成
- HTML
  - ページの構造を作成
- CSS
    - ページの見た目を整える
- JavaScript
    - ページの動きを制御

### Vue.jsとは
- Webページ上の要素の操作や通信処理などをシンプルな記述で行えるJavaScriptのフレームワーク

### Vue.jsの基本的な構文
- 1つのファイルに3つの言語を書く

```vue
<!--JavaScript-->
<script setup>
  import { ref } from 'vue'
  const count = ref(0)
</script> 

<!--HTML-->
<template>
  <button @click="count++">
    Count is: {{ count }}
  </button>
</template>

<!--CSS-->
<style scoped>
  button {
    font-weight: bold;
  }
</style>
```
### ここからの進め方
- サンプルコードがコピーできるので、[Vue SFC Playground](https://play.vuejs.org/#eNp9kUFLwzAUx79KfJcqzA3R0+gGKgP1oKKCl1xG99ZlpklIXmah9Lv7krLqQXbr+//+L/2l7eDWuekhIsyhDJVXjkRAim4pjWqc9SQ64XErerH1thEFVwtppKmsCSSaUItF4ufFA2ptxaf1enNWXEhTzobj+CAeCBun14Q8CVHurpZdl5f7vpzxlFNlXCRxuGzsBvVCAnMJjMrZuA0ToMCv3qp6ug/WsHWXdiVUtnFKo39xpFhNwlxkktiazb6fckY+4uSYVzusvv7J96FNmYRXjwH9ASWMjNa+Rhrw6v0ZW34eIZtHze0T8A2D1TE5DrW7aDas/aeXbR/zt1em/girltCE46WSaGr2uS+B/8f9iav/6l5Pb/KeND30P7yHphw=)で確認しながら進めよう

