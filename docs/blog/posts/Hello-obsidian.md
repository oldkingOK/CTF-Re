---
draft: true
date: 2023-04-21
slug: 搭建Obsidian博客从入门到入土
categories:
  - Hello
  - World
tags:
  - how-to
---

## 从模板创建仓库

[jobindjohn/obsidian-publish-mkdocs: A Template to Publish Obsidian/Foam Notes on Github Pages (uses MkDocs)](https://github.com/jobindjohn/obsidian-publish-mkdocs)

## 已知问题

1. 图片无法设定大小
	- 使用代码 `![[Pulana2.jpg|PunalaJam|220x400]]` 本地可以显示
	- 但是部署之后会显示 `{ width="220"; height="400" }`
	- 然后大小和页面一样大
2. 博客必须从模板创建
	- 即 `/blog/posts` 里面的文件必须带有 `Properties`