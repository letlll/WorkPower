LibreOffice 可以通过命令行使用，它的命令行工具非常适合批量文件转换或自动化任务。以下是使用 LibreOffice 命令行工具的步骤：

### Windows 上使用 LibreOffice 命令行工具

1. **找到 LibreOffice 的安装路径**：
   - 默认情况下，LibreOffice 安装在 `C:\Program Files\LibreOffice\program` 目录下。

2. **打开命令提示符（CMD）或 PowerShell**：
   - 按 `Win + R`，然后输入 `cmd` 并回车。
   - 或者按 `Win + X`，选择 “Windows PowerShell”。

3. **导航到 LibreOffice 安装目录**：
   你可以通过以下命令将目录切换到 LibreOffice 安装路径：
   ```bash
   cd "C:\Program Files\LibreOffice\program"
   ```

4. **执行 LibreOffice 命令行工具**：
   使用 `soffice` 命令，这是 LibreOffice 命令行工具的入口程序。例如，以下命令将 `.pptx` 文件转换为 `.pdf` 文件：

   ```bash
   soffice --headless --convert-to pdf your_presentation.pptx
   ```

   - `--headless`：表示不启动图形界面，只在后台运行。
   - `--convert-to`：指定目标格式，可以是 `pdf`、`docx`、`txt`、`html` 等。
   - `your_presentation.pptx`：这是你要转换的文件的路径。

### 常见命令行选项

- **转换文件格式**：
  ```bash
  soffice --headless --convert-to [目标格式] [文件名]
  ```

  例如，将 `.docx` 文件转换为 `.pdf`：
  ```bash
  soffice --headless --convert-to pdf your_document.docx
  ```

- **打开文件**（不启动图形界面的情况下处理文件）：
  ```bash
  soffice --headless your_file.odt
  ```

- **指定输出目录**：
  如果你想将输出文件保存到特定目录，可以使用 `--outdir` 参数：
  ```bash
  soffice --headless --convert-to pdf your_presentation.pptx --outdir C:\output_directory
  ```

### Linux 或 macOS 上使用 LibreOffice 命令行工具

1. **在终端中打开命令行**：
   - 按 `Ctrl + Alt + T` 打开终端（Linux）。
   - 或者在 `Applications -> Utilities` 中找到 `Terminal`（macOS）。

2. **直接运行 LibreOffice 命令**：
   在 Linux 和 macOS 上，LibreOffice 通常默认安装在全局路径中，命令是 `libreoffice` 或 `soffice`。你可以直接运行命令：

   ```bash
   libreoffice --headless --convert-to pdf your_presentation.pptx
   ```

### 常见格式转换

- **将 `.docx` 转换为 `.pdf`**：
  ```bash
  soffice --headless --convert-to pdf your_document.docx
  ```

- **将 `.pptx` 转换为 `.txt`**：
  ```bash
  soffice --headless --convert-to txt your_presentation.pptx
  ```

- **将 `.odt` 转换为 `.html`**：
  ```bash
  soffice --headless --convert-to html your_document.odt
  ```

### 查看所有命令行选项
要查看 LibreOffice 所有可用的命令行选项，可以运行以下命令：

```bash
soffice --help
```

这会列出所有可用的命令行参数和它们的解释。

通过这些步骤，你可以轻松地使用 LibreOffice 的命令行工具来处理各种文档和格式转换任务。