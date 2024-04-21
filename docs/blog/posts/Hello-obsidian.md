---
draft: false
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

## 常见问题

### 如何本地构建？

[Instructions to build locally using mkdocs · Issue #1 · jobindjohn/obsidian-publish-mkdocs (github.com)](https://github.com/jobindjohn/obsidian-publish-mkdocs/issues/1)

```shell
python -m venv xxx
# activate
pip install -r requirements.txt
```

```
mkdocs
mkdocs-material
mkdocs-mermaid2-plugin
mkdocs-ezlinked-plugin
mkdocs-awesome-pages-plugin
mdx_breakless_lists
mkdocs-preview-links-plugin
mkdocs-embed-file-plugins
Markdown
obs2mk
mkdocs-roamlinks-plugin
PyYAML
setuptools
```
### 博客必须从模板创建

即 `/blog/posts` 里面的文件必须带有 `Properties`

## 如何修改页面内容

[Customization - Material for MkDocs (squidfunk.github.io)](https://squidfunk.github.io/mkdocs-material/customization/)
## 已知问题

1. 图片无法设定大小
	- 使用代码 `![[Pulana2.jpg|PunalaJam|220x400]]` 本地可以显示
	- 但是部署之后会显示 `{ width="220"; height="400" }`
	- 然后大小和页面一样大

解决办法 [python - mkdocs: how to attach a downloadable file - Stack Overflow](https://stackoverflow.com/questions/76275641/mkdocs-how-to-attach-a-downloadable-file)

即修改 `mkdocs.yml`,

```
markdown_extensions:
  - attr_list
```

然后就可以用了

同样解决的还有 `[file.ext](../static/file.ext){:download="文件名.txt"}` 这样可以直接下载文件了

例如：`[样本](./static/DebugMe.apk){:download="DebugMe.apk"}`