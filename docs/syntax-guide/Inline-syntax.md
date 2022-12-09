# 内联语法

## 优先级

大多数内联语法由围绕内联内容的开始和结束定界符（`delimiters`）定义，例如将其定义为强调或链接文本。控制内联容器“优先级”的基本原则是第一个关闭的开启器（开始定界符）优先。容器不能重叠，所以一旦开启器被关闭，开启器和关闭器之间的任何潜在开启器都会被标记为常规文本，并且不能再触发内联语法解析。例如：

|`_This is *regular_ not strong* emphasis`|
|---|
|`<p><em>This is *regular</em> not strong* emphasis</p>`|

!!! info "说明"
    如上，第一行为源文本，第二行为预期的 Djot 解析结果。

第一个 `_` 打开的常规强调被第二个 `_` 关闭，此时第一个 `*` 不再有资格打开强强调（`strong emphasis`）。但是在：

|`This is _strong* not regular_ emphasis`|
|---|
|`<p><strong>This is _strong</strong> not regular_ emphasis</p>`|

此时，结果恰恰相反。

相似地：

|`[Link *](url)*`|
|---|
|`<p><a href="url">Link *</a>*</p>`|

包含一个链接时，

|`*Emphasis [*](url)`|
|---|
|`<p><strong>Emphasis [</strong>](url)</p>`|

则不会如此（因为强调结束于 `[` 定界符）。

尽管排除了重叠容器，但也可以使用**嵌套**容器，如下：

|`_This is *strong within* regular emphasis_`|
|---|
|`<p><em>This is <strong>strong within</strong> regular emphasis</em></p>`|

在歧义的情况下，`{` 和 `}` 可用于将定界符标记为开始定界符或结束定界符。因此 `{_` 表现得像 `_` 但只能开始强调，而 `_}` 表现得像 `_` 但只能结束强调：


|`{_Emphasized_}`</br>`_}not emphasized{_`|
|---|
|`<p><em>Emphasized</em>`</br>`_}not emphasized{_</p>`|

使用大括号显式标记的关闭器只能匹配显式标记的开启器，而非标记的关闭器只能匹配非标记的开启器（例如，`{_hi_)` 不会产生强调）。

当有多个开启器可能与给定的关闭器匹配时，Djot 将使用最接近的开瓶器。例如：

|`*not strong *strong*`|
|---|
|`<p>*not strong <strong>strong</strong></p>`|

## 普通文本

任何没有赋予特殊含义的内容都会被解析为文字文本。

所有 ASCII 标点字符（甚至那些在 djot 中没有特殊含义的字符）都可以反斜杠转义。因此，`\*` 包含文字字符 `*`。除 ASCII 标点符号字符外的字符前的反斜杠仅被视为普通的文字反斜杠，但以下情况除外：

- 换行符前（或换行符后的空格或制表符前）的反斜杠会被解析为硬换行符。在这种情况下，反斜杠前的空格和制表符将被忽略。
- 被解析为不间断空格的空格之前的反斜杠。

## 链接

Djot 有两种链接，内联链接（`Inline links`）和参考链接（`reference links`）。

两种类型都以 `[ … ]` 分隔符内的链接文本（可能包含任意内联格式）开头。

内联链接的链接目标（`URL`）应放置在紧随其后的圆括号中，`]` 和 `(` 之前不能存在空格。如下：

|`[My link text](http://example.com)`|
|---|
|`<p><a href="http://example.com">My link text</a></p>`|

URL 可以分成多行；在这种情况下，换行符和任何前导和尾随空格都将被忽略，并且这些行被连接在一起。

```
[My link text](http://example.com?product_number=234234234234  
234234234234)
```
```
<p><a href="http://example.com?product_number=234234234234234234234234">My link text</a></p>
```

参考链接使用方括号中的参考标签，而不是圆括号中的目的地（URL）。这必须紧接在链接文本之后：

|`[My link text][foo bar]`</p>`[foo bar]: http://example.com`|
|---|
|`<p><a href="http://example.com">My link text</a></p>`|

参考标签应在文档中的某处定义：请参阅下面的参考链接定义。但是，链接的解析是“本地”的，不依赖于标签是否被定义：

|`[foo][bar]`|
|---|
|`<p><a>foo</a></p>`|

如果标签为空，则链接文本将被视为参考标签以及链接文本：

|`[My link text][]`</p>`[My link text]: /url`|
|---|
|`<p><a href="/url">My link text</a></p>`|

## 图像

图像与链接相似，但有一个 `!` 前缀。与链接一样，内联和引用变体都是可能的。

|`![picture of a cat](cat.jpg)`</p>`![picture of a cat][cat]`</p>`![cat][]`</p>`[cat]: feline.jpg`|
|---|
|`<p><img alt="picture of a cat" src="cat.jpg"></p>`</p>`<p><img alt="picture of a cat" src="feline.jpg"></p>`</p>`<p><img alt="cat" src="feline.jpg"></p>`|

## 自动链接

包含在 `<…>` 中的 URL 或电子邮件地址将被超链接。尖括号之间的内容按字面意思处理（不能使用反斜杠转义）。

|`<https://pandoc.org/lua-filters>`</p>`<me@example.com>`|
|---|
|`<p><a href="https://pandoc.org/lua-filters">https://pandoc.org/lua-filters</a>`</p>`<a href="mailto:me@example.com">me@example.com</a></p>`|

URL 或电子邮件地址不能包含换行符。

## 逐字文本

逐字内容（`Verbatim content`）以一串连续的反引号字符（`）开头，以等长的连续反引号字符字符串结束。

```
``Verbatim with a backtick` character``
`Verbatim with three backticks ``` character`
```
```
<p><code>Verbatim with a backtick` character</code>
<code>Verbatim with three backticks ``` character</code></p>
```

反引号之间的内容将被视为逐字文本（反斜杠转义在这里不起作用）。

如果内容以反引号字符开始或结束，则在开始或结束反引号与内容之间删除一个空格。

```
`` `foo` ``
```
```
<p><code>`foo`</code></p>
```

如果要解析为内联的文本在遇到结束反引号字符串之前结束，则逐字文本扩展到末尾。

|<code>`foo bar</code>|
|---|
|`<p><code>foo bar</code></p>`|

## 斜体与加粗

斜体的内联内容由 `_` 字符分隔。加粗（`Strong emphasis`）由 `*` 字符分隔。

!!! note "注意"
    中文中并没有使用斜体作为强调文本的习惯。

|`_emphasized text_`</p>`*strong emphasis*`|
|---|
|`<p><em>emphasized text</em></p>`</p>`<p><strong>strong emphasis</strong></p>`|
|<p><em>emphasized text</em></p><p><strong>strong emphasis</strong></p>|

`_` 或 `*` 只有在其后没有直接跟空格时才可以开始强调；`_` 或 `*` 只有在前面不直接有空格，并且在开启器和关闭器之间除了定界符之外还有其他字符的情况下，它才能关闭强调。

|`_ Not emphasized (spaces). _`</p>`___ (not an emphasized `_` character)`|
|---|
|`<p>_ Not emphasized (spaces). _</p>`</p>`<p>___ (not an emphasized <code>_</code> character)</p>`|

强调可以嵌套：

|`__emphasis inside_ emphasis_`|
|---|
|`<p><em><em>emphasis inside</em> emphasis</em></p>`|
|<p><em><em>emphasis inside</em> emphasis</em></p>|

花括号可用于强制将 `_` 或 `*` 解释为开启器或关闭器。

|`{_ this is emphasized, despite the spaces! _}`|
|---|
|`<p><em> this is emphasized, despite the spaces! </em></p>`|
|<p><em> this is emphasized, despite the spaces! </em></p>|

## 高亮

`{=` 和 `=}` 之间的内联内容将被视为突出显示的文本（在 HTML 中，为 `<mark>`）。请注意，`{` 和 `}` 是强制性的。

|`This is {=highlighted text=}.`|
|---|
|`<p>This is <mark>highlighted text</mark>.</p>`|
|<p>This is <mark>highlighted text</mark>.</p>|

## 上标与下标

上标由 `^` 字符分隔，下标由 `~` 分隔。

|`H~2~O and djot^TM^`|
|---|
|`<p>H<sub>2</sub>O and djot<sup>TM</sup></p>`|
|<p>H<sub>2</sub>O and djot<sup>TM</sup></p>|


可以使用花括号，但不是必需的：

|`H{~one two buckle my shoe~}O`|
|---|
|`<p>H<sub>one two buckle my shoe</sub>O</p>`|
|<p>H<sub>one two buckle my shoe</sub>O</p>|

## 下划线/删除

要将内联文本标记为插入（下划线），请使用 `{+` 和 `+}`。要将其标记为已删除，请使用 `{-` 和 `-}`。`{` 和 `}` 是强制性的。

|`My boss is {-mean-}{+nice+}.`|
|---|
|`<p>My boss is <del>mean</del><ins>nice</ins>.</p>`|
|<p>My boss is <del>mean</del><ins>nice</ins>.</p>|

## 智能标点符号

Djot 将直双引号 (") 和单引号 (') 解析为弯引号。Djot 非常擅长从上下文中找出需要引号的方向。

|"'Shelob' is my name."</p>"'Shelob' is my name."|
|---|
|`<p>&ldquo;Hello,&rdquo; said the spider.`</p>`&ldquo;&lsquo;Shelob&rsquo; is my name.&rdquo;</p>`|
|<p>&ldquo;Hello,&rdquo; said the spider.</p>&ldquo;&lsquo;Shelob&rsquo; is my name.&rdquo;</p>|

但是，你也可以通过使用大括号将引号标记为开启器 `{"` 或关闭器 `"}` 来覆盖它的启发式方法：

|`'}Tis Socrates' season to be jolly!`|
|---|
|`<p>&rsquo;Tis Socrates&rsquo; season to be jolly!</p>`|
|<p>&rsquo;Tis Socrates&rsquo; season to be jolly!</p>|

如果你想要直接引用，请使用反斜杠转义：

|`5\'11\"`|
|---|
|`<p>5'11"</p>`|
|<p>5'11"</p>|

三个句点的序列被解析为省略号。

三个连字符的序列被解析为破折号。

两个连字符的序列被解析为破折号。

|`57--33 oxen---and no sheep...`|
|---|
|`<p>57&ndash;33 oxen&mdash;and no sheep&hellip;</p>`|
|<p>57&ndash;33 oxen&mdash;and no sheep&hellip;</p>|

更长的连字符序列分为 [em-dashes](https://en.wikipedia.org/wiki/Dash#Em_dash)、[en-dashes](https://en.wikipedia.org/wiki/Dash#En_dash) 和[连字符](https://en.wikipedia.org/wiki/Hyphen)。（因此，4 个连字符变成两个 `en-dashes`，而 6 个连字符变成两个 `em-dashes`）。

|`a----b c------d`|
|---|
|`<p>a&ndash;&ndash;b c&mdash;&mdash;d</p>`|
|<p>a&ndash;&ndash;b c&mdash;&mdash;d</p>|

## 数学

Djot 支持 [LaTeX](https://www.latex-project.org/) 数学；请将数学文本放在逐字范围内，并在其前面加上 `$`（用于内联数学）或 `$$`（用于显示数学）：

|``` Einstein derived $`e=mc^2`. ```</p>`Pythagoras proved`</p>``` $$` x^n + y^n = z^z ` ```|
|---|
|`<p>Einstein derived <span class="math inline">\(e=mc^2\)</span>.`</p>`Pythagoras proved`</p>`<span class="math display">\[ x^n + y^n = z^z \]</span></p>`|

## 脚注参考

脚注参考是 `^` `+` 方括号中的参考标签。

```
Here is the reference.[^foo]
[^foo]: And here is the note.
```
```
<p>Here is the reference.<a id="fnref1" href="#fn1" role="doc-noteref"><sup>1</sup></a></p>
<section role="doc-endnotes">
<hr>
<ol>
<li id="fn1">
<p>And here is the note.<a href="#fnref1" role="doc-backlink">↩︎︎</a></p>
</li>
</ol>
</section>
```

有关脚注本身的语法，请参见[块语法](./Block-syntax.md)中的脚注。

## 换行

内联内容中的换行符被视为“软”换行符；它们可能呈现为空格，或者（在换行符在语义上被视为空格的上下文中，例如 HTML）作为换行符。

要获得硬换行符（由 HTML 的 `<br>` 表示的那种），请使用反斜杠 + 换行符：


|`This is a soft`</p>`break and this is a hard\`</p>`break.`|
|---|
|`<p>This is a soft`</p>`break and this is a hard<br>`</p>`break.</p>`|

## 注释

属性中两个 `%` 字符之间的材料将被忽略并被视为注释。这允许将注释添加到属性：

`{#ident % later we'll add a class %}`

但它也可以作为添加评论的通用方式。只需使用仅包含注释的属性说明符：

|`Foo bar {% This is a comment, spanning multiple lines %} baz.`|
|---|
|`<p>Foo bar  baz.</p>`|

## Emoji

你可以通过在别名周围加上 `:` 符号来包含表情符号：

|`My reaction is :+1: :smiley:.`|
|---|
|`<p>My reaction is 👍 😃.</p>`|

## 原始内联

你可以使用逐字跨度后跟 `{=FORMAT}` 来包含任何格式的原始内联内容：

|``This is `<?php echo 'Hello world!' ?>`{=html}.``|
|---|
|``<p>This is <?php echo 'Hello world!' ?>.</p>``|

此内容旨在在呈现指定格式时逐字传递，否则将被忽略。

## Span

方括号中不是链接或图像且紧跟属性的文本被视为通用范围。

|`It can be helpful to [read the manual]{.big .red}.`|
|---|
|`<p>It can be helpful to <span class="big red">read the manual</span>.</p>`|

## 内联属性

属性放在大括号内，并且必须**紧跟在**它们所附加的内联元素之后（中间没有空格）。

在大括号内，可以使用以下语法：

- `.foo` 将 `foo` 指定为一个类。可以通过这种方式给出多个类；他们将合并。
- `#foo` 将 `foo` 指定为标识符。一个元素可能只有一个标识符；如果给出多个标识符，则使用最后一个。
- `key="value"` 或 `key=value` 指定键值属性（`key-value attribute`）。当值完全由 ASCII 字母数字字符或 `_` 或 `:` 或 `-` 组成时，不需要引号。反斜杠转义可以用在引用值中。
- `%` 表示开始注释，以下一个 `%` 或属性（`}`）结尾。

属性说明符可以包含换行符。

例如：

|`An attribute on _emphasized text_{#foo`</p>`.bar .baz key="my value"}`|
|---|
|`<p>An attribute on <em class="bar baz" id="foo" key="my value">emphasized text</em></p>`|

属性说明符可以“堆叠”，在这种情况下它们将被组合。因此，

|`avant{lang=fr}{.blue}`|
|---|
|`<p><span class="blue" lang="fr">avant</span></p>`|

与以下相同：

|`avant{lang=fr .blue}`|
|---|
|`<p><span class="blue" lang="fr">avant</span></p>`|