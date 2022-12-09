# 块语法

与 commonmark 一样，块结构可以在内联解析之前被识别，并且优先于内联结构。

事实上，块可以没有回溯地逐行解析。单行对于块级结构的贡献从不依赖于外部的行。

缩进仅对列表项或脚注嵌套有意义。

块级项目应该用空行彼此分开。在某些情况下，两个块级元素可以相邻——例如，主题分隔符或围栏代码块后面可以直接跟一个段落。事实上，逐行解析的可能性排除了在块级元素之后需要一个空行。但为了可读性，我们建议始终用空行分隔块级元素。段落永远不能被其他块级元素打断，并且必须始终以空行（或文档或包含元素的末尾）结尾。

## 段落

段落（`paragraph`）是不满足成为其他任意块级元素的条件的非空文本行序列。文本内容被解析为一系列内联元素。换行符被视为软换行并被解释为格式化输出中的空格。段落以空行或文档结尾结束。

## 标题

标题以一个或多个 `#` 字符序列开头，后跟空格。`#` 字符的数量定义标题级别。以下文本被解析为内联内容。

|`## A level _two_ heading!`|
|---|
|`<section id="A-level-two-heading">`</p>`<h2>A level <em>two</em> heading!</h2>`</p>`</section>`|

标题文本可能会溢出到后续行，这些行之前也可能有相同数量的 `#` 字符（但也可以省略）。

当遇到空行（或文档或封闭容器的末尾）时，标题结束。

```
# A heading that
# takes up
# three lines

A paragraph, finally
```
```
<section id="A-heading-that-takes-up-three-lines">
<h1>A heading that
takes up
three lines</h1>
<p>A paragraph, finally</p>
</section>
```

或

```
# A heading that
takes up
three lines

A paragraph, finally.
```
```
<section id="A-heading-that-takes-up-three-lines">
<h1>A heading that
takes up
three lines</h1>
<p>A paragraph, finally.</p>
</section>
```

## 块引用

块引用是一系列行，每一行都以 `>` 开头，后跟一个空格或行尾。块引用的内容（减去首字母 `>`）被解析为块级内容。

```
> This is a block quote.
>
> 1. with a
> 2. list in it.
```
```
<blockquote>
<p>This is a block quote.</p>
<ol>
<li>
with a
</li>
<li>
list in it.
</li>
</ol>
</blockquote>
```

与在 Markdown 中一样，你可以“懒惰地”省略块引用内常规段落行中的 `>` 前缀，但在段落的第一行前面的 `>` 不能省略：

```
> This is a block
quote.
```
```
<blockquote>
<p>This is a block
quote.</p>
</blockquote>
```

## 列表项

相对于列表标记缩进，列表项由列表标记、空格（或换行符）和一行或多行组成，例如：

```
1.  This is a
 list item.

 > containing a block quote
```
```
<ol>
<li>
<p>This is a
list item.</p>
<blockquote>
<p>containing a block quote</p>
</blockquote>
</li>
</ol>
```

段落第一行之后的段落行可以“懒惰地”省略缩进：

```
1.  This is a
list item.

  Second paragraph under the
list item.
```
```
<ol>
<li>
<p>This is a
list item.</p>
<p>Second paragraph under the
list item.</p>
</li>
</ol>
```

Djot 可以使用以下基本类型的列表标记：

|标记符号|列表类型|
|---|---|
|-|无序|
|+|无序|
|*|无序|
|1.|有序，十进制枚举，后跟句点|
|1)|有序，十进制枚举，后跟句点|
|(1)|有序，十进制枚举，后跟句点|
|a|有序的，小写字母枚举，后跟句点|
|a)|有序的，小写字母枚举，后跟句点|
|(a)|有序的，小写字母枚举，后跟句点|
|A.|有序，大写字母枚举，后跟句点|
|A)|有序，大写字母枚举，后跟句点|
|(A)|有序，大写字母枚举，后跟句点|
|i.|有序的，小写罗马数字枚举，后跟句点|
|i)|有序的，小写罗马数字枚举，后跟句点|
|(i)|有序的，小写罗马数字枚举，后跟句点|
|I.|有序的，大写罗马数字枚举，后跟句点|
|I)|有序的，大写罗马数字枚举，后跟句点|
|(I)|有序的，大写罗马数字枚举，后跟句点|
|:|定义|
|- [ ]|任务|

有序列表标记可以使用系列中的任何数字：因此，`(xix)` 和 `v)` 都是有效的小写罗马数字枚举标记，并且 `v)` 也是有效的小写字母枚举标记。

### 任务列表项

以 `[ ]`、`[X]` 或 `[x]` 开头后跟空格的项目符号列表项是任务列表项，有未选中 (`[ ]`) 和已选中（`[X]` 或 `[x]`）两种状态。

### 定义列表项

在定义列表项中，`:` 标记之后的第一行或多行文本被解析为内联内容并被视为定义的术语。 项目中包含的任何其他块都被假定为定义。

```
: orange

  A citrus fruit.
```
```
<dl>
<dt>orange</dt>
<dd>
<p>A citrus fruit.</p>
</dd>
</dl>
```

## 列表

列表只是一系列相同类型的列表项（上表中的每一行都定义了一个类型）。请注意，更改有序列表样式或项目符号将停止一个列表并开始一个新列表。因此，以下列表项被分为四个不同的列表：

```
i) one
i. one (style change)
+ bullet
* bullet (style change)
```
```
<ol start="9" type="a">
<li>
one
</li>
</ol>
<ol start="9" type="a">
<li>
one (style change)
</li>
</ol>
<ul>
<li>
bullet
</li>
</ul>
<ul>
<li>
bullet (style change)
</li>
</ul>
```

有时列表项的类型不明确。在这种情况下，如果可能的话，将以继续列表的方式解决歧义。例如：

```
i. item
j. next item
```
```
<ol start="9" type="a">
<li>
item
</li>
<li>
next item
</li>
</ol>
```

第一项的类型不确定是 lower-roman-enumerated（有序小写罗马数字枚举） 或 lower-alpha-enumerated（有序小写字母枚举）。但只有后一种解释适用于下一项，因此我们更喜欢允许我们拥有一个连续列表而不是两个单独列表的阅读。

有序列表的起始编号将由其第一项的编号确定。后续项目的数量无关紧要。

```
5) five
8) six
```
```
<ol start="5">
<li>
five
</li>
<li>
six
</li>
</ol>
```

如果列表在项目之间或项目内部的块之间不包含空行，则该列表被归类为紧密列表（`tight list`）。列表开头或结尾的空行不计入紧度。

```
- one
- two

  - sub
  - sub
```
```
<ul>
<li>
one
</li>
<li>
two
<ul>
<li>
sub
</li>
<li>
sub
</li>
</ul>
</li>
</ul>
```

不紧的清单是松（`loose`）的。 这种区别的预期意义在于，在呈现紧凑列表时，项目之间的空间应该更小。

```
- one

- two
```
```
<ul>
<li>
<p>one</p>
</li>
<li>
<p>two</p>
</li>
</ul>
```

## 代码块

代码块以一行三个或更多个连续的反引号开始，你可以选择后跟一个语言说明符，但没有其他内容（语言说明符可以选择在前面或后面加上空格）。

代码块以一行反引号结束，长度等于或大于开始反引号“围栏”，如果没有遇到这样的行，则以文档结束或块的末尾作为代码块的结束。

Djot 会将代码块的内容解释为逐字文本。如果块内容包含一行反引号，请务必选择更长的反引号字符串用作“围栏”：

``````
````
This is how you do a code block:

``` ruby
x = 5 * 6
```
````
``````

`````````
<pre><code>This is how you do a code block:

``` ruby
x = 5 * 6
```
</code></pre>
`````````

这是一个代码块的示例，当其父容器关闭时，该代码块将隐式关闭：

````````
> ```
> code in a
> block quote

Paragraph.
````````

```
<blockquote>
<pre><code>code in a
block quote
</code></pre>
</blockquote>
<p>Paragraph.</p>
```

## 主题中断

包含三个或更多 `*` 或 `-` 字符且没有其他任何内容（空格或制表符除外）的行被视为主题中断（在 HTML 中表示为 `<hr>`）。与 Markdown 不同，主题中断可以缩进：

```
Then they went to sleep.

      * * * *

When they woke up, ...
```
```
<p>Then they went to sleep.</p>
<hr>
<p>When they woke up, &hellip;</p>
```

## 原始块

带有 `=FORMAT` 的代码块通常会被解释为 `FORMAT` 中的原始内容，并将逐字传递以该格式输出。例如：

`````
``` =html
<video width="320" height="240" controls>
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.ogg" type="video/ogg">
  Your browser does not support the video tag.
</video>
```
`````

```
<video width="320" height="240" controls>
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.ogg" type="video/ogg">
  Your browser does not support the video tag.
</video>
```

## div

一个 div 以一行三个或更多个连续的冒号开头，后面可以选择空格和类名（但没有其他内容）。它以至少与开头围栏一样长的一行连续冒号结束，或者以文档或包含块的末尾结束。

div 的内容被解释为块级内容。

```
::: warning
Here is a paragraph.

And here is another.
:::
```

```
<div class="warning">
<p>Here is a paragraph.</p>
<p>And here is another.</p>
</div>
```

## Pipe table（图表）

Pipe table 由一系列行组成。每行以管道字符 (`|`) 开头和结尾，并包含一个或多个由管道字符分隔的单元格：

```
| 1 | 2 |
```

```
<table>
<tr>
<td>1</td>
<td>2</td>
</tr>
</table>
```

分隔线是一行，其中每个单元格由一个或多个 `-` 字符的序列组成，可选地以 `:` 字符作为前缀或后缀的 Pipe table。

当遇到分隔线时，前一行将被视为标题，并且该行和任何后续行的对齐方式由分隔线确定（直到找到新标题）。分隔线不会成为表格的一行。

```
| fruit  | price |
|--------|------:|
| apple  |     4 |
| banana |    10 |
```
```
<table>
<tr>
<th>fruit</th>
<th style="text-align: right;">price</th>
</tr>
<tr>
<td>apple</td>
<td style="text-align: right;">4</td>
</tr>
<tr>
<td>banana</td>
<td style="text-align: right;">10</td>
</tr>
</table>
```

列对齐由分隔线按以下方式确定：

- 如果 `-` 行以 `:` 开头且不以 `:` 结尾，则该列左对齐；
- 如果以 `:` 结尾且不以 `:` 开头，则该列右对齐；
- 如果它以 `:` 开头和结尾，则该列居中对齐；
- 如果它既不以 `:` 开头也不以 `:` 结尾，则该列默认对齐。

```
| a  |  b |
|----|:--:|
| 1  | 2  |
|:---|---:|
| 3  | 4  |
```
```
<table>
<tr>
<th>a</th>
<th style="text-align: center;">b</th>
</tr>
<tr>
<th style="text-align: left;">1</th>
<th style="text-align: right;">2</th>
</tr>
<tr>
<td style="text-align: left;">3</td>
<td style="text-align: right;">4</td>
</tr>
</table>
```

如上，带有 a 和 b 的行是标题，左列默认对齐，右列居中对齐。下一个实际行，包含 1 和 2，也是一个标题，其中左列左对齐，右列右对齐。此对齐方式也适用于随后的行。

表格不需要有标题，也可省略任何分隔线，或者（如果你需要指定列对齐方式）以分隔线开头：

```
|:--|---:|
| x | 2  |
```
```
<table>
<tr>
<td style="text-align: left;">x</td>
<td style="text-align: right;">2</td>
</tr>
</table>
```

表格单元格的内容被解析为内联。Pipe table 的单元格中不可能有块级内容。

Djot 足够聪明，可以逐字识别反斜杠转义的管道和管道；这些不算作单元格分隔符：

```
| just two \| `|` | cells in this table |
```

```
<table>
<tr>
<td>just two | <code>|</code></td>
<td>cells in this table</td>
</tr>
</table>
```

## 参考链接定义

参考链接定义由方括号中的参考标签、冒号、空格（或换行符）和 URL 组成。 URL 可以分成多行（在这种情况下，这些行将被连接起来，并删除任何前导或尾随空格）。

```
[google]: https://google.com

[product page]: http://example.com?item=983459873087120394870128370
  0981234098123048172304
```

Djot 没有对引用标签进行大小写规范化，定义为 [link] 的引用不能用作 [Link]，而在 Markdown 中可以这么做。

```
{title=foo}
[ref]: /url

[ref][]
```
```
<p><a href="/url" title="foo">ref</a></p>
```

上述示例生成了一个带有文本 “ref”、URL `/url` 和标题 “foo” 的链接。但是，如果在链接和引用定义上都定义了相同的属性，则链接上的属性会覆盖引用定义上的属性。

```
{title=foo}
[ref]: /url

[ref][]{title=bar}
```
```
<p><a href="/url" title="bar">ref</a></p>
```

在这里我们得到一个标题为 “bar” 的链接。

## 脚注

脚注由一个脚注引用和一个冒号组成，后面是脚注的内容；脚注的内容需要缩进到脚注引用开始的那一行后面。注释的内容被解析为块级内容。

```
Here's the reference.[^foo]

[^foo]: This is a note
  with two paragraphs.

  Second paragraph.

  > a block quote in the note.
```

```
<p>Here&rsquo;s the reference.<a id="fnref1" href="#fn1" role="doc-noteref"><sup>1</sup></a></p>
<section role="doc-endnotes">
<hr>
<ol>
<li id="fn1">
<p>This is a note
with two paragraphs.</p>
<p>Second paragraph.</p>
<blockquote>
<p>a block quote in the note.</p>
</blockquote>
<p><a href="#fnref1" role="doc-backlink">↩︎︎</a></p>
</li>
</ol>
</section>
```

与块引号和列表项一样，段落中的后续行可以“懒惰地”省略缩进：

```
Here's the reference.[^foo]

[^foo]: This is a note
with two paragraphs.

  Second paragraph must
be indented, at least in the first line.
```
```
<p>Here&rsquo;s the reference.<a id="fnref1" href="#fn1" role="doc-noteref"><sup>1</sup></a></p>
<section role="doc-endnotes">
<hr>
<ol>
<li id="fn1">
<p>This is a note
with two paragraphs.</p>
<p>Second paragraph must
be indented, at least in the first line.<a href="#fnref1" role="doc-backlink">↩︎︎</a></p>
</li>
</ol>
</section>
```

## 块属性

要将属性附加到块级元素，请将属性放在块之前的行中。块属性与内联属性具有相同的语法，但它们必须放在一行中。可以使用重复的属性说明符，属性会累积。

```
{#water}
{.important .large}
Don't forget to turn off the water!

{source="Iliad"}
> Sing, muse, of the wrath of Achilles
```
```
<p class="important large" id="water">Don&rsquo;t forget to turn off the water!</p>
<blockquote source="Iliad">
<p>Sing, muse, of the wrath of Achilles</p>
</blockquote>
```

## 标题链接

标识符会自动添加到没有附加明确标识符的任何标题中。标识符是通过获取标题的文本内容，删除标点符号（`_` 和 `-` 除外），将空格替换为 `-`，如果需要唯一性，添加数字后缀。

```
## My heading + auto-identifier
```
```
<section id="My-heading-auto-identifier">
<h2>My heading + auto-identifier</h2>
</section>
```

但是，在大多数情况下，您不需要知道分配给标题的标识符，因为 Djot 会为所有标题创建隐式链接引用。因此，要链接到同一文档中标题为 “Introduction” 的标题，可以简单地使用参考链接：

```
See the [Epilogue][].

    * * * *

# Epilogue
```

```
<p>See the <a href="#Epilogue">Epilogue</a>.</p>
<hr>
<section id="Epilogue">
<h1>Epilogue</h1>
</section>
```

## 嵌套限制

符合规范的实现可以对嵌套施加合理的限制，以避免堆栈溢出或其他问题。很少有现实文档需要超过 12 层嵌套，因此 512 层的限制应该是绝对安全的。