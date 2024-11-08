如果你的 Markdown 编辑器不直接支持 LaTeX 语法，可以尝试以下几种方式来显示公式：

### 1. 使用带图片的公式展示
如果 Markdown 编辑器不支持 LaTeX，但支持图片插入，你可以将公式转化为图片插入。可以使用在线工具（如 [math.now.sh](https://math.now.sh/) 或 [LaTeX to PNG](https://latex2png.com/)）生成公式图片，再插入到 Markdown 文件中：

```markdown
![factorial formula](https://latex.codecogs.com/png.latex?n!%20%3D%20%5Cbegin%7Bcases%7D%20%201%20%26%20%5Ctext%7Bif%20%7D%20n%20%3D%200%20%5C%5C%20%20n%20%5Ccdot%20%28n%20-%201%29%21%20%26%20%5Ctext%7Bif%20%7D%20n%20%3E%200%20%20%5Cend%7Bcases%7D)
```

这段代码会插入一个图片链接，使用 LaTeX 公式格式通过 `latex.codecogs.com` 渲染。这样即使 Markdown 不支持 LaTeX，也可以通过图片显示公式。

### 2. 使用 MathJax（若支持插件）
一些 Markdown 编辑器支持 **MathJax** 插件，可以用来渲染 LaTeX 公式。启用 MathJax 后，在 Markdown 中可以这样写：

```markdown
$$
n! = 
\begin{cases} 
1 & \text{if } n = 0 \\
n \cdot (n - 1)! & \text{if } n > 0 
\end{cases}
$$
```

如果你的 Markdown 编辑器有 MathJax 支持（比如在一些博客平台或 Notion 中），这段代码将渲染成公式格式。

### 3. 使用纯文本格式表示
如果 Markdown 仅支持纯文本，可以简单表示为：

```markdown
n! = { 1,               if n = 0
       n * (n - 1)!,    if n > 0 }
```

这种方式虽然没有格式化的美观效果，但在没有其他选项时，也能清楚表达公式的递归定义。