# Welcome!

!!! warning "注意"
    - 你当前所看到的内容还处于非常早期的制作阶段。  
    - 欢迎提交 Issue 或 PR 协助改进站点内容。

>[Djot](https://github.com/jgm/djot) 是一种轻量标记语法。它的大部分功能来自 [commonmark](https://commonmark.org/)，但它修正了一些使 commonmark 语法复杂和难以有效解析的地方。它的功能也比 commonmark 全面得多，支持定义列表、脚注、表格、几种新的内联格式（插入、删除、高亮、上标、下标）、数学、智能标点符号、可应用于任何元素的属性，以及块级、内联级和原始内容的通用容器。这个项目开始是为了实现我在《[Beyond Markdown](https://johnmacfarlane.net/beyond-markdown.html)》中建议的一些想法。  
>—— [John MacFarlane](https://johnmacfarlane.net/index.html)

- [设计目标和对设计决定的解释](./prepare/rationale.md)
- [语法描述](./syntax-guide/index.md)
- [Markdown 用户的快速入门](./syntax-guide/markdown-quick-switch.md)
- [Live Sandbox](https://djot.net/playground/)：在线尝试 Djot
- [源代码](https://github.com/jgm/djot)，包括：
    - 一个非常快速的纯 Lua 解析器；  
    - 自定义 pandoc 阅读器，允许将 djot 文档转换为 pandoc 支持的任何格式；  
    - 自定义 pandoc writer，允许将 pandoc 支持的任何格式的文档转换为 djot。  
- 命令行工具 `djot` 使用手册

使用 [luarocks](https://luarocks.org/) 在本地安装解析器：

```
$ git clone https://github.com/jgm/djot
$ cd djot
$ luarocks install --local djot
$ djot -h
```

## 其他

- [安装 Djot](./prepare/install.md)
- [术语表](./about/Glossary.md)
- [许可证清单](./about/licenses-list.md)