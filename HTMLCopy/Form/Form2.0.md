[代码模板](../../效率/代码模板.md)

```latex
\section{设计 HTML 结构}


\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

<!DOCTYPE html>

<html lang="zh">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>问卷调查表</title>

  <link rel="stylesheet" href="styles.css">

</head>

<body>

  

<div class="container">

  <h2>问卷调查表</h2>

  <form id="surveyForm">

    <div class="form-group">

      <label for="name">姓名 <span style="color: red;">*</span></label>

      <input type="text" id="name" name="name" placeholder="请输入您的姓名" required>

      <small>请填写您的真实姓名。</small>

    </div>

  

    <div class="form-group">

      <label>性别 <span style="color: red;">*</span></label>

      <input type="radio" id="male" name="gender" value="male" required>

      <label for="male">男</label>

      <input type="radio" id="female" name="gender" value="female" required>

      <label for="female">女</label>

      <input type="radio" id="other" name="gender" value="other" required>

      <label for="other">其他</label>

      <small>请选择您的性别。</small>

    </div>

  

    <div class="form-group">

      <label for="age">年龄 <span style="color: red;">*</span></label>

      <input type="text" id="age" name="age" placeholder="请输入您的年龄" required>

      <small>请输入您的实际年龄（数字）。</small>

    </div>

  

    <div class="form-group">

      <label>兴趣爱好 <span style="color: red;">*</span></label>

      <input type="checkbox" id="reading" name="hobby" value="reading">

      <label for="reading">阅读</label>

      <input type="checkbox" id="traveling" name="hobby" value="traveling">

      <label for="traveling">旅行</label>

      <input type="checkbox" id="sports" name="hobby" value="sports">

      <label for="sports">运动</label>

      <input type="checkbox" id="music" name="hobby" value="music">

      <label for="music">音乐</label>

      <small>请选择您的兴趣爱好（可多选）。</small>

    </div>

  

    <div class="form-group">

      <label for="feedback">其他建议或意见</label>

      <textarea id="feedback" name="feedback" rows="4" placeholder="请提供您的建议或意见"></textarea>

      <small>您的反馈将帮助我们改进。</small>

    </div>

  

    <button type="submit">提交</button>

  </form>

</div>

  

<script src="script.js"></script>

  

</body>

</html>

\end{lstlisting}

\section{编写 CSS 样式}


\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

body {

    font-family: Arial, sans-serif;

    background-color: #f4f4f4;

    margin: 0;

    padding: 20px;

}

  

.container {

    max-width: 600px;

    margin: auto;

    background: white;

    padding: 20px;

    border-radius: 5px;

    box-shadow: 0 2px 10px rgba(0,0,0,0.1);

}

  

h2 {

    text-align: center;

}

  

.form-group {

    margin-bottom: 15px;

}

  

label {

    display: block;

    margin-bottom: 5px;

}

  

input[type="text"], textarea {

    width: 100%;

    padding: 10px;

    border: 1px solid #ccc;

    border-radius: 4px;

}

  

input[type="radio"], input[type="checkbox"] {

    margin-right: 10px;

}

  

button {

    background-color: #28a745;

    color: white;

    padding: 10px 15px;

    border: none;

    border-radius: 5px;

    cursor: pointer;

    width: 100%;

}

  

button:hover {

    background-color: #218838;

}

\end{lstlisting}

\section{实现 JavaScript 功能}


\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

document.getElementById('surveyForm').addEventListener('submit', function(event) {

    // 检查所有必填字段是否填写

    const requiredFields = this.querySelectorAll('[required]');

    let allFilled = true;

  

    requiredFields.forEach(field => {

        if (!field.value) {

            allFilled = false;

            field.style.borderColor = 'red';

        } else {

            field.style.borderColor = '';

        }

    });

  

    if (!allFilled) {

        event.preventDefault();

        alert('请确保所有必填项都已填写。');

    }

});

\end{lstlisting}

```