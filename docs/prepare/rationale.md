# 基本原理

## 设计目标

1. 应该可以在线性时间内解析 djot 标记，没有回溯。
2. 内联元素的解析应该是“本地的”，而不是依赖于后面引用的定义。  
    - 在 commonmark 中情况并非如此：`[foo][bar]` 可能是后跟带有文本 “bar” 的链接的 “[foo]”，或 “[foo][bar]”，或带有文本 “foo” 的链接，或文本 “foo” 后跟 “[bar]” 的链接，这取决于引用 [foo] 和 [bar] 是否在文档的其他地方（可能稍后）定义。这种非局部性使得准确的语法高亮几乎是不可能的。
3. 强调的规则应该更简单。  
    - 在 commonmark 中使用双字符来强强调（加粗）的事实导致许多潜在的歧义，这些歧义由 17 条令人生畏的，复杂的规则列表解决。大多数时候，它们以人类最自然地解释文本的方式来解释文本——但并非总是如此。
4. 应避免表达盲点。  
    - 在 commonmark 中，如果你想生成 HTML `a<em>?</em>b`，你就不走运了，因为侧翼规则将 `a*?*b` 中的第一个星号分类为右翼。虽然有办法解决这个问题，但它很难看（使用数字实体而不是 `a`）。在 djot 中不应该有这种表现力的盲点。
5. 哪些内容属于列表项的规则应该很简单。  
    - 在 commonmark 中，列表项下的内容必须缩进到列表标记后的第一个非空格内容（或标记后五个空格，以防列表项以缩进代码开头）。当他们的缩进内容缩进不够远并且没有包含在列表项中时，许多人会感到困惑。
6. 不应强制解析器识别 unicode 字符类、HTML 标记或实体，或执行 unicode 大小写折叠。这增加了很多复杂性。
7. 语法应该对硬换行[^1]友好：  
    - 硬换行段落不应导致不同的解释，例如当一个数字后跟一个句点在一行的开头结束时。  
    我预计很多人会问，为什么要硬换行？  
    回答：这样您的文档就可以按原样阅读，无需转换为 HTML，也无需使用软换行的特殊编辑器模式。请记住，源代码的可读性是 Markdown 和 Commonmark 的主要目标之一。
8. 从以下意义上讲，语法应该统一组合：  
   - 如果一系列行在列表项或块引号之外具有某种含义，那么它在其中也应该具有相同的含义。这个原则在 commonmark 规范中[有明确阐述](https://spec.commonmark.org/0.30/#principle-of-uniformity)，但规范并未完全遵守它[^2]。
9. 应该可以将任意属性附加到任何元素。
10. 应该有文本、内联内容和块级内容的通用容器，可以应用任意属性。这允许使用 AST 转换进行扩展。
11. 语法应尽可能简单，以符合这些目标。因此，例如我们不需要两种不同样式的标题或代码块。

## 设计决定

这些目标激发了以下决定：

- 由于目标 7，块级元素不能中断段落（或标题）。因此在 djot 中，以下是单个段落，而不是（如 commonmark 所见）段落后跟有序列表，后跟块引号，后跟节标题：
```
My favorite number is probably the number
1. It's the smallest natural number that is
> 0. With pencils, though, I prefer a
# 2.
```
Commonmark 确实对目标 7 做出了一些让步，禁止以 `1.` 以外的标记开头的列表打断段落。但这是对语法规则性和可预测性的妥协和牺牲。最好只是有一个一般规则。  
- 最后一个决定的含义是，虽然“紧凑”列表仍然是可能的（项目之间没有空行），但子列表必须始终以空行开头。因此，而不是
```
- Fruits
  - apple
  - orange
```  
你需要写成：  
```  
- Fruits

  - apple
  - orange
```  
（此空行不计入“紧度（`tightness`）”。）reStructuredText 做出相同的设计决定。
- 同样为了促进目标 7，我们允许标题“懒惰地”跨越多行：
```  
## My excessively long section heading is too
long to fit on one line.
```  
当我们这样做时，我们将通过删除 setext 样式（带下划线）的标题来简化语法。我们真的不需要两种标题语法（目标 11）。  
- 为了实现目标 5，我们有一个非常简单的规则：缩进超出列表标记开头的任何内容都属于列表项。  
```  
1. list item

  > block quote inside item 1

2. second item
```  
在 commonmark 中，这将被解析为两个单独的列表，它们之间有一个块引号，因为块引号缩进得不够远。 阻止我们在 commonmark 中使用这个简单规则的是缩进代码块。 如果列表项要包含缩进代码块，我们需要知道从哪一列开始计算缩进，所以我们固定在使列表看起来最好的列（标记后非空格内容的第一列）：  
```
1.  A commonmark list item with an indented code block in it.

        code!
```  
在 djot 中，我们只是去掉了缩进的代码块。 无论如何，大多数人更喜欢围栏代码块，我们不需要两种不同的代码块编写方式（目标 11）。  
- 为了实现目标 6 并避免采用 commonmark 处理原始 HTML 使用复杂的规则，我们根本不允许原始 HTML，除非在明确标记的上下文中，例如 `` `<a id="foo">`{=html}``
或者    
````
``` =html
<table>
<tr><td>foo</td></tr>
</table>
```
````  
与 Markdown 不同，djot 不是以 HTML 为中心的。Djot 文档可能会呈现为各种不同的格式，因此尽管我们希望提供以任何输出格式包含原始内容的灵活性，但没有理由赋予 HTML 特权。出于类似的原因，我们不像 commonmark 那样解释 HTML 实体。  
- 为了实现目标 2，我们将引用链接在本地解析。任何看起来像 `[foo][bar]` 或 `[foo][]` 的东西都被视为参考链接，无论 `[foo]` 是否在文档的后面定义。一个推论是我们必须摆脱快捷链接语法，只有一对括号，`[像这样]`。必须始终清楚什么是链接，而无需了解周围的上下文。
- 为了实现目标 6，参考链接不再区分大小写。在 ASCII 上下文之外支持这一点需要在每个实现中构建 unicode 大小写折叠，这似乎没有必要。
- 块状引号中的 `>` 后面需要一个空格或换行，以避免违反目标 8 中指出的统一性原则，如：
```
>This is not a
>block quote in djot.
```
- 为了实现目标 3，我们避免使用双字符来强调重点。相反，我们使用 `_` 表示斜体，`*` 表示加粗。强调可以从这些字符之一开始，只要它后面没有空格，当遇到类似的字符时结束，只要后一个字符前面没有空格并且中间出现了一些不同的字符。在重叠的情况下，第一对完成闭合的字符对优先解析。（这个简单的规则也避免了我们在 commonmark 中确定 unicode 字符类的需要——目标 6。）
- 就其本身而言，这最后的变化会引入一些表现力盲点。例如，基于简单规则：
```
_(_foo_)_
```
它会被解析成：
```
<em>(</em>foo<em>)</em>
```
而不是：  
```
<em>(<em>foo</em>)</em>
```
如果你想要后一种解释，djot 允许你使用以下语法：
```
_({_foo_})_
```
`{_` 是只能开始斜体的 `_`，而 `_}` 是只能结束斜体的 `_`。相似地，你在使用 `*` 或其他任何在起始字符和结束字符之间解析并不明确的内联格式化标记时，可以使用 `{` 和 `}` 完成对于解析区域的界定。某些内联标记需要这些大括号，例如 `{=高亮文本=}`、`{+插入文本+}` 和 `{-删除文本-}`，因为字符 `=`、`+` 和 `-` 经常出现在普通文本中。
- 为了支持目标 1，代码跨行解析不会回溯。 因此，如果你打开一个代码范围但不关闭它，它会延伸到该段的末尾。这类似于围栏代码块在 commonmark 中的工作方式。
```
This is `inline code.
```
- 为了支持目标 9，djot 引入了通用属性语法。属性可以附加到任何块级元素，方法是将属性放在目标元素前面的行中，也可以附加到任何行内级元素（方法是将它们直接放在它后面）：
```
{#introduction}
This is the introductory paragraph, with
an identifier `introduction`.

           {.important color="blue" #heading}
## heading

The word *atelier*{weight="600"} is French.
```
- 由于我们将拥有通用属性，因此我们不再支持链接中引用的标题。如果需要，可以添加 title 属性，但这不是很常见，因此我们不需要特殊的语法：
```
[Link text](url){title="Click me!"}
```
- Fenced div 和 bracketed span 的引入是为了允许将属性附加到块级或内联级元素的任意序列。例如，
```
{#warning .sidebar}
::: Warning
This is a warning.
Here is a word in [français]{lang=fr}.
:::
```

[^1]: [Difference between hard wrap and soft wrap? - Stackoverflow](https://stackoverflow.com/questions/319925/difference-between-hard-wrap-and-soft-wrap#:~:text=A%20hard%20wrap%20inserts%20actual%20line%20breaks%20in,but%20looks%20like%20it%27s%20divided%20into%20several%20lines.)
[^2]: 参见 [commonmark/commonmark-spec#634](https://github.com/commonmark/commonmark-spec/issues/634)