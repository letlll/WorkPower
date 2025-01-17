---
tags:
  - MDwriterTools
---
要在 Typora 中生成目录，可以按照以下步骤操作：

1. ==**在文档中插入 `[TOC]` 标签**：==
   - 在你希望目录出现的位置，输入以下内容：
     ```
     [TOC]
     ```
     这将根据你的文档标题自动生成目录。

2. **确保使用正确的标题格式**：
   - 目录是根据 Markdown 的标题级别生成的，请确保你的标题使用了正确的语法，例如：
     ```
     # 一级标题
     ## 二级标题
     ### 三级标题
     ```
   - 正确的标题格式有助于目录准确地反映文档结构。

3. **检查 Typora 设置**：
   - 点击菜单栏中的“**文件**” > “**偏好设置**”（或按 `Ctrl+,`）。
   - 在偏好设置窗口中，选择“**Markdown**”选项卡。
   - 确保勾选了“**在 [TOC] 中启用目录**”或类似选项。

4. **刷新目录**：
   - 如果修改了标题或新增了内容，目录可能不会自动更新。
   - 你可以保存文档或者右键点击目录区域，选择“**刷新**”来更新目录。

5. **自定义目录样式（可选）**：
   - 如果你希望修改目录的显示样式，可以编辑 Typora 的主题文件。
   - 打开“**偏好设置**” > “**外观**” > “**打开主题文件夹**”。
   - 在对应的 CSS 文件中，添加或修改与目录相关的样式。

**注意**：在某些版本中，可能需要使用 `[toc]` 或 `[[toc]]` 来生成目录。如果 `[TOC]` 无效，可以尝试这些变体。