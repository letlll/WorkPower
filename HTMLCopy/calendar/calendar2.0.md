```latex
\section{设计 HTML 结构}


\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

<!DOCTYPE html>

<html lang="en">

<head>

  <meta charset="UTF-8">

  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Document</title>

  <link rel="stylesheet" href="shizhong.css">

  <title>电子日历</title>

</head>

<body>

  <div id="calendar">

    <h2 id="month-year"></h2>

    <div id="days"></div>

    <button id="prev">上个月</button>

    <button id="next">下个月</button>

  

  </div>

  <script src="shizhong.js"></script>

</body>

</html>

\end{lstlisting}

\section{编写 CSS 样式}



\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

body {

    font-family: Arial, sans-serif;

    background-color: #f9f9f9;

    margin: 0;

    padding: 20px;

}

  

#calendar {

    background-color: #ffffff;

    border-radius: 10px;

    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);

    padding: 20px;

    max-width: 400px;

    margin: auto;

}

  

h2 {

    text-align: center;

    margin: 0 0 20px;

    color: #333;

}

  

#days {

    overflow: hidden;

}

  

.day {

    float: left;

    width: 14.28%;

    background-color: #e9e9e9;

    padding: 15px;

    border-radius: 5px;

    text-align: center;

    transition: background-color 0.3s;

    margin: 5px 0; /* 添加上下间距 */

    font-size: 18px;

    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1); /* 增加阴影 */

}

  

.day:hover {

    background-color: #d0d0d0;

}

  

.today {

    background-color: #ffcc00;

    font-weight: bold;

    border-radius: 5px;

    box-shadow: 0 4px 10px rgba(255, 204, 0, 0.5); /* 突出当前日期 */

}

  

button {

    background-color: #007bff;

    color: white;

    border: none;

    border-radius: 5px;

    padding: 10px 15px;

    cursor: pointer;

    margin: 10px 5px;

    transition: background-color 0.3s;

    font-size: 16px;

}

  

button:hover {

    background-color: #0056b3;

}

\end{lstlisting}

\section{实现 JavaScript 功能}


\begin{lstlisting}[language=HTML, linewidth=\textwidth, breaklines=true]

const monthYear = document.getElementById('month-year');

const daysContainer = document.getElementById('days');

let currentDate = new Date();

  

function renderCalendar() {

    const year = currentDate.getFullYear();

    const month = currentDate.getMonth();

    monthYear.textContent = currentDate.toLocaleString('default', { month: 'long', year: 'numeric' });

    daysContainer.innerHTML = '';

  

    const firstDay = new Date(year, month, 1).getDay();

    const lastDate = new Date(year, month + 1, 0).getDate();

  

    // Create empty spaces for the first day of the month

    // Remove the line generating empty spaces

    // daysContainer.innerHTML += '<div class="day"></div>'.repeat(firstDay);

    // Create day elements

    for (let i = 1; i <= lastDate; i++) {

        const isToday = (i === new Date().getDate() && month === new Date().getMonth() && year === new Date().getFullYear());

        daysContainer.innerHTML += `<div class="day ${isToday ? 'today' : ''}">${i}</div>`;

    }

}

  

document.getElementById('prev').onclick = () => {

    currentDate.setMonth(currentDate.getMonth() - 1);

    renderCalendar();

};

  

document.getElementById('next').onclick = () => {

    currentDate.setMonth(currentDate.getMonth() + 1);

    renderCalendar();

};

  

renderCalendar();

\end{lstlisting}

```