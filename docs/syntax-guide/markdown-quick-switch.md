# Markdown 用户的快速入门

Djot 很像 Markdown。以下是你在进行过渡时需要注意的一些主要差异。

## 空行

在 djot 中，您需要在块级元素周围留空行。因此，请勿

```
This is some text.
## My next heading
```

而是写成：

```
This is some text.

## My next heading
```

请误写成：

`````
This is some text.
``` lua
local foo = bar.baz or false
```
`````

而是写成：

`````
This is some text.

``` lua
local foo = bar.baz or false
```
`````

请勿写成：

```
Text.
> a blockquote.
```

而是写成：

```
Text.

> a blockquote.
```

请勿写成：

```
Before a thematic break.
****
After a thematic break.
```

而是写成：

```
Before a thematic break.

****

After a thematic break.
```

## 列表

一个特例是列表前总是需要一个空行，即使它是一个子列表。 所以，在 Markdown 中你可以写：

```
- one
  - two
  - three
```

在 Djot 中，你需要写成：

```
- one

  - two
  - three
```

## 标题

Djot 没有 See Text 样式（下划线）标题，只有 ATX- (`#`) 样式。

标题内容可以扩展到多行，前面可能有也可能没有 `#` 字符：

```
## This is a single
## level-2 heading

### This is a single
level-3 heading
```

因此，标题后面必须始终有一个空行。

标题中的尾随 `#` 字符将作为内容的一部分读取，不会被忽略。

## 代码块

没有缩进的代码块，只能用 ``` 围起来。

## 块引用

`>` 字符后需要一个空格，除非其后跟换行符。

## 强调

Djot 使用单个 `_` 分隔符表示常规强调（斜体），使用单个 `*` 分隔符表示强烈强调（加粗）。

## 链接

没有为链接添加标题的特殊语法，如 Markdown：

```
[link](http://example.com "Go to my website")
```

如果你想在链接上使用 title 属性，请使用通用属性语法：

```
[link](http://example.com){title="Go to my website"}
```

## 硬换行符

在 Markdown 中，您可以通过以两个空格结束一行来创建硬换行符。在 djot 中，你可在换行符之前使用反斜杠。

```
A new\
line.
```

## 原始 HTML

在 Markdown 中，您可以“按原样”插入原始 HTML。在 djot 中，您必须将其标记为原始 HTML：

`````
This is raw HTML: `<a id="foo">`{=html}.

Here is a raw HTML block:

``` =html
<table>
<tr><td>foo</td></tr>
</table>
```
`````

## 表格

与许多 Markdown 实现不同，Pipe tables 在每行的开头和结尾始终需要一个管道字符。所以，这不是一张表格：

```
a|b
-|-
1|2
```

而应写成：

```
| a | b |
| - | - |
| 1 | 2 |
```

这足以开始！

在这里，我们只关注可能会绊倒 Markdown 用户的事情。 如果您牢记这些，您应该能够开始使用 djot 而无需查看任何更多文档。

但是，我们还没有讨论您可以在 djot 中而不是 Markdown 中做的任何事情。请参阅[语法描述](./index.md)以了解可用的新结构。