# 实验报告

## 3. 实验步骤

### 3.1 设计 HTML 结构

首先，设计页面的基本结构，使用 HTML 创建表单和 Canvas 元素，以便用户输入数据并展示图形。

```html

<!DOCTYPE html>

<html lang="zh">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>统计数据可视化</title>

    <link rel="stylesheet" href="styles.css">

</head>

<body>

    <div class="container">

        <h1>统计数据可视化</h1>

        <form id="dataForm">

            <input type="text" id="label" placeholder="数据标签" required>

            <input type="number" id="value" placeholder="数据值" required min="0">

            <button type="submit">添加数据</button>

        </form>

        <button id="drawBarChart">绘制柱状图</button>

        <button id="drawPieChart">绘制饼状图</button>

        <canvas id="barChartCanvas" width="400" height="300"></canvas>

        <canvas id="pieChartCanvas" width="400" height="300"></canvas>

    </div>

    <script src="script.js"></script>

</body>

</html>

```

### 3.2 编写 CSS 样式

通过 CSS 美化页面，确保输入框、按钮和图形的美观和易用性。

```css

body {

    font-family: Arial, sans-serif;

    background-color: #f4f4f4;

    margin: 0;

    padding: 20px;

}

.container {

    max-width: 600px;

    margin: auto;

    padding: 20px;

    background: #fff;

    border-radius: 8px;

    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);

}

h1 {

    text-align: center;

}

form {

    display: flex;

    justify-content: space-between;

}

input {

    padding: 10px;

    margin: 5px;

    border: 1px solid #ccc;

    border-radius: 4px;

    flex: 1;

}

button {

    padding: 10px;

    margin: 5px;

    border: none;

    border-radius: 4px;

    background-color: #28a745;

    color: white;

    cursor: pointer;

}

button:hover {

    background-color: #218838;

}

canvas {

    display: block;

    margin: 20px auto;

}

```

### 3.3 实现 JavaScript 功能

使用 JavaScript 实现数据收集和图形绘制的逻辑，包括对用户输入的验证和图形的动态绘制。

```javascript

const barChartCanvas = document.getElementById('barChartCanvas');

const pieChartCanvas = document.getElementById('pieChartCanvas');

const ctxBar = barChartCanvas.getContext('2d');

const ctxPie = pieChartCanvas.getContext('2d');

let data = [];

document.getElementById('dataForm').addEventListener('submit', (event) => {

    event.preventDefault();

    const label = document.getElementById('label').value.trim();

    const value = parseInt(document.getElementById('value').value);

    if (label && !isNaN(value) && value >= 0) {

        data.push({ label, value });

        document.getElementById('label').value = '';

        document.getElementById('value').value = '';

    } else {

        alert("请输入有效的标签和非负值");

    }

});

document.getElementById('drawBarChart').addEventListener('click', drawBarChart);

document.getElementById('drawPieChart').addEventListener('click', drawPieChart);

function drawBarChart() {

    ctxBar.clearRect(0, 0, barChartCanvas.width, barChartCanvas.height);

    const barWidth = (barChartCanvas.width / data.length) * 0.8;

    const maxBarHeight = barChartCanvas.height - 20;

    const scale = maxBarHeight / Math.max(...data.map(d => d.value));

    data.forEach((item, index) => {

        const barHeight = item.value * scale;

        const x = index * (barWidth + 10) + 10;

        const y = barChartCanvas.height - barHeight;

        ctxBar.fillStyle = '#4CAF50';

        ctxBar.fillRect(x, y, barWidth, barHeight);

        ctxBar.fillStyle = '#000';

        ctxBar.fillText(item.label, x, barChartCanvas.height - 5);

        ctxBar.fillText(item.value, x, y - 5);

    });

}

function drawPieChart() {

    ctxPie.clearRect(0, 0, pieChartCanvas.width, pieChartCanvas.height);

    const total = data.reduce((sum, item) => sum + item.value, 0);

    let startAngle = 0;

    data.forEach(item => {

        const sliceAngle = (item.value / total) * 2 * Math.PI;

        ctxPie.beginPath();

        ctxPie.moveTo(pieChartCanvas.width / 2, pieChartCanvas.height / 2);

        ctxPie.arc(pieChartCanvas.width / 2, pieChartCanvas.height / 2, 100, startAngle, startAngle + sliceAngle);

        ctxPie.closePath();

        ctxPie.fillStyle = getRandomColor();

        ctxPie.fill();

        startAngle += sliceAngle;

        const labelX = pieChartCanvas.width / 2 + Math.cos(startAngle - sliceAngle / 2) * 60;

        const labelY = pieChartCanvas.height / 2 + Math.sin(startAngle - sliceAngle / 2) * 60;

        ctxPie.fillStyle = '#000';

        ctxPie.fillText(`${item.label}: ${item.value}`, labelX, labelY);

    });

}

function getRandomColor() {

    const letters = '0123456789ABCDEF';

    let color = '#';

    for (let i = 0; i < 6; i++) {

        color += letters[Math.floor(Math.random() * 16)];

    }

    return color;

}

```

# LATEX

```latex
\chapter{实验报告}

\section{实验步骤}

\subsection{设计 HTML 结构}

首先，设计页面的基本结构，使用 HTML 创建表单和 Canvas 元素，以便用户输入数据并展示图形。

\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>统计数据可视化</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="container">
        <h1>统计数据可视化</h1>
        <form id="dataForm">
            <input type="text" id="label" placeholder="数据标签" required>
            <input type="number" id="value" placeholder="数据值" required min="0">
            <button type="submit">添加数据</button>
        </form>
        <button id="drawBarChart">绘制柱状图</button>
        <button id="drawPieChart">绘制饼状图</button>
        <canvas id="barChartCanvas" width="400" height="300"></canvas>
        <canvas id="pieChartCanvas" width="400" height="300"></canvas>
    </div>
    <script src="script.js"></script>
</body>
</html>
\end{lstlisting}

\subsection{编写 CSS 样式}

通过 CSS 美化页面，确保输入框、按钮和图形的美观和易用性。

\begin{lstlisting}[language=CSS]
/* 样式总体布局 */
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 20px;
}
.container {
    max-width: 600px;
    margin: auto;
    padding: 20px;
    background: #fff;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}
h1 {
    text-align: center;
}
form {
    display: flex;
    justify-content: space-between;
}
input {
    padding: 10px;
    margin: 5px;
    border: 1px solid #ccc;
    border-radius: 4px;
    flex: 1;
}
button {
    padding: 10px;
    margin: 5px;
    border: none;
    border-radius: 4px;
    background-color: #28a745;
    color: white;
    cursor: pointer;
}
button:hover {
    background-color: #218838;
}
canvas {
    display: block;
    margin: 20px auto;
}
\end{lstlisting}

\subsection{实现 JavaScript 功能}

使用 JavaScript 实现数据收集和图形绘制的逻辑，包括对用户输入的验证和图形的动态绘制。

\begin{lstlisting}[language=JavaScript]
const barChartCanvas = document.getElementById('barChartCanvas');
const pieChartCanvas = document.getElementById('pieChartCanvas');
const ctxBar = barChartCanvas.getContext('2d');
const ctxPie = pieChartCanvas.getContext('2d');
let data = [];

document.getElementById('dataForm').addEventListener('submit', (event) => {
    event.preventDefault();
    const label = document.getElementById('label').value.trim();
    const value = parseInt(document.getElementById('value').value);
    if (label && !isNaN(value) && value >= 0) {
        data.push({ label, value });
        document.getElementById('label').value = '';
        document.getElementById('value').value = '';
    } else {
        alert("请输入有效的标签和非负值");
    }
});

document.getElementById('drawBarChart').addEventListener('click', drawBarChart);
document.getElementById('drawPieChart').addEventListener('click', drawPieChart);

function drawBarChart() {
    ctxBar.clearRect(0, 0, barChartCanvas.width, barChartCanvas.height);
    const barWidth = (barChartCanvas.width / data.length) * 0.8;
    const maxBarHeight = barChartCanvas.height - 20;
    const scale = maxBarHeight / Math.max(...data.map(d => d.value));
    data.forEach((item, index) => {
        const barHeight = item.value * scale;
        const x = index * (barWidth + 10) + 10;
        const y = barChartCanvas.height - barHeight;
        ctxBar.fillStyle = '#4CAF50';
        ctxBar.fillRect(x, y, barWidth, barHeight);
        ctxBar.fillStyle = '#000';
        ctxBar.fillText(item.label, x, barChartCanvas.height - 5);
        ctxBar.fillText(item.value, x, y - 5);
    });
}

function drawPieChart() {
    ctxPie.clearRect(0, 0, pieChartCanvas.width, pieChartCanvas.height);
    const total = data.reduce((sum, item) => sum + item.value, 0);
    let startAngle = 0;
    data.forEach(item => {
        const sliceAngle = (item.value / total) * 2 * Math.PI;
        ctxPie.beginPath();
        ctxPie.moveTo(pieChartCanvas.width / 2, pieChartCanvas.height / 2);
        ctxPie.arc(pieChartCanvas.width / 2, pieChartCanvas.height / 2, 100, startAngle, startAngle + sliceAngle);
        ctxPie.closePath();
        ctxPie.fillStyle = getRandomColor();
        ctxPie.fill();
        startAngle += sliceAngle;
        const labelX = pieChartCanvas.width / 2 + Math.cos(startAngle - sliceAngle / 2) * 60;
        const labelY = pieChartCanvas.height / 2 + Math.sin(startAngle - sliceAngle / 2) * 60;
        ctxPie.fillStyle = '#000';
        ctxPie.fillText(`${item.label}: ${item.value}`, labelX, labelY);
    });
}

function getRandomColor() {
    const letters = '0123456789ABCDEF';
    let color = '#';
    for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
    }
    return color;
}
\end{lstlisting}

```