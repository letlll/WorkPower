```latex
\documentclass{report}
\usepackage{graphicx}
\usepackage{listings}
\usepackage{xcolor}

% 设置代码样式
\lstset{
    language=HTML,
    basicstyle=\ttfamily\small,
    keywordstyle=\color{blue},
    commentstyle=\color{gray},
    stringstyle=\color{red},
    breaklines=true,
    numbers=left,
    numberstyle=\tiny\color{gray},
    frame=single,
    captionpos=b
}

\begin{document}

\chapter{实验报告}

\section{实验步骤}

\subsection{项目初始化}

\begin{enumerate}
    \item \textbf{创建项目文件夹}：在本地创建一个名为\texttt{PokerStraightGame}的文件夹，作为项目的根目录。
    \item \textbf{创建基本文件结构}：
    \begin{itemize}
        \item 在根目录下创建\texttt{index.html}，\texttt{styles.css}，\texttt{script.js}三个主要文件。
        \item 创建\texttt{sounds/}文件夹，存放音效文件（\texttt{success.mp3}和\texttt{fail.mp3}）。
        \item 如果使用卡牌图像，创建\texttt{images/}文件夹，存放卡牌图片。
    \end{itemize}
\end{enumerate}

\subsection{编写HTML结构}

\begin{enumerate}[resume]
    \item \textbf{设置文档类型和语言}：
    \begin{lstlisting}[language=HTML, caption=设置文档类型和语言]
    <!DOCTYPE html>
    <html lang="zh-CN">
    \end{lstlisting}
    
    \item \textbf{定义页面头部}：
    \begin{itemize}
        \item 设置字符编码为UTF-8。
        \item 配置视口以实现响应式设计。
        \item 设置内容安全策略（CSP）以增强页面安全性。
        \item 引入外部CSS文件。
        \item 设置页面标题为“扑克牌顺子游戏”。
    \end{itemize}
    
    \item \textbf{构建页面主体结构}：
    \begin{itemize}
        \item \textbf{头部 (\texttt{header})}：
        \begin{itemize}
            \item 显示拖动次数、得分和倒计时。
            \item 添加音效开关按钮。
        \end{itemize}
        \item \textbf{主游戏区 (\texttt{main})}：
        \begin{itemize}
            \item \textbf{A框}：初始扑克牌区域，玩家可以拖动扑克牌。
            \item \textbf{B框}：目标扑克牌区域，设置5个卡槽用于放置扑克牌。
            \item 添加重置按钮，允许玩家重新开始游戏。
        \end{itemize}
        \item \textbf{游戏结束弹窗 (\texttt{endgame-modal})}：游戏结束时显示最终得分和拖动次数，提供重玩按钮。
        \item \textbf{提示信息框 (\texttt{message-box})}：用于显示临时提示信息。
        \item \textbf{音效文件引用}：引入成功和失败的音效文件。
        \item \textbf{引入外部JavaScript文件}。
    \end{itemize}
    
    \item \textbf{完整HTML代码}：
    \begin{lstlisting}[language=HTML, caption=完整的index.html文件]
    <!DOCTYPE html>
    <html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';">
        <title>扑克牌顺子游戏</title>
        <link rel="stylesheet" href="styles.css">
    </head>
    <body>
        <!-- 页面顶部 -->
        <header>
            <div id="drag-count">拖动次数：0</div>
            <div id="score">得分：0</div> <!-- 新增得分显示 -->
            <div id="timer">时间：60秒</div>
            <div id="sound-toggle" title="静音/开启音效">🔊</div>
        </header>
    
        <!-- 主游戏区 -->
        <main>
            <!-- A 框：初始扑克牌区域 -->
            <div id="a-frame" class="card-frame">
                <div class="frame-title">A 框：拖动扑克牌到此处</div>
                <div id="deck" class="deck">
                    <!-- 扑克牌将通过 JavaScript 动态生成 -->
                </div>
                <div id="a-frame-tooltip" class="tooltip">请将扑克牌拖入 B 框</div>
            </div>
    
            <!-- 间距 -->
            <div class="spacer"></div>
    
            <!-- B 框：目标扑克牌区域 -->
            <div id="b-frame" class="card-frame">
                <div class="frame-title">B 框：拖动5张连续扑克牌到此处</div>
                <div id="target" class="target">
                    <!-- 卡槽1到卡槽5 -->
                    <div class="card-slot" data-slot="1"></div>
                    <div class="card-slot" data-slot="2"></div>
                    <div class="card-slot" data-slot="3"></div>
                    <div class="card-slot" data-slot="4"></div>
                    <div class="card-slot" data-slot="5"></div>
                </div>
                <div id="b-frame-tooltip" class="tooltip">将5张牌放置到此框中，并满足顺子要求</div>
                <button id="reset-button">重置游戏 🔄</button>
            </div>
        </main>
    
        <!-- 游戏结束弹窗 -->
        <div id="endgame-modal" class="modal hidden">
            <div class="modal-content">
                <h2>游戏结束！</h2>
                <p id="final-count">你的拖动次数是 0 次！</p>
                <button id="replay-button">重玩</button>
            </div>
        </div>
    
        <!-- 顺子提示信息 -->
        <div id="message-box" class="message-box hidden"></div>
    
        <!-- 音效文件 -->
        <audio id="success-sound" src="sounds/success.mp3" preload="auto"></audio>
        <audio id="fail-sound" src="sounds/fail.mp3" preload="auto"></audio>
    
        <script src="script.js"></script>
    </body>
    </html>
    \end{lstlisting}
\end{enumerate}

\subsection{编写CSS样式}

\begin{enumerate}[resume]
    \item \textbf{全局样式}：
    \begin{itemize}
        \item 设置页面的背景颜色、字体、边距和填充。
    \end{itemize}
    
    \item \textbf{头部样式 (\texttt{header})}：
    \begin{itemize}
        \item 使用Flex布局实现拖动次数、得分、倒计时和音效开关的水平排列。
        \item 设置固定定位，使其始终显示在页面顶部。
    \end{itemize}
    
    \item \textbf{主游戏区样式 (\texttt{main})}：
    \begin{itemize}
        \item 使用Flex布局将A框和B框并排显示。
        \item 设置A框和B框的样式，包括背景颜色、边框、圆角、宽高等。
    \end{itemize}
    
    \item \textbf{扑克牌和卡槽样式}：
    \begin{itemize}
        \item 定义扑克牌的大小、背景颜色、边框、圆角和字体样式。
        \item 设置卡槽的样式，确保其能够接收拖放的扑克牌。
    \end{itemize}
    
    \item \textbf{提示信息和弹窗样式}：
    \begin{itemize}
        \item 定义提示信息框和游戏结束弹窗的样式，确保其在需要时显示，并具有良好的可读性和美观性。
    \end{itemize}
    
    \item \textbf{按钮样式}：
    \begin{itemize}
        \item 定义重置按钮和重玩按钮的样式，确保其易于点击和操作。
    \end{itemize}
    
    \item \textbf{响应式设计}：
    \begin{itemize}
        \item 使用媒体查询调整不同屏幕尺寸下的布局和元素大小，确保游戏在移动设备和桌面设备上均能良好显示。
    \end{itemize}
    
    \item \textbf{完整CSS代码}：
    \begin{lstlisting}[language=CSS, caption=完整的styles.css文件]
    /* 样式总体布局 */
    body {
        font-family: Arial, sans-serif;
        background-color: #2E7D32;
        color: #FFFFFF;
        margin: 0;
        padding: 0;
    }
    
    header {
        display: flex;
        justify-content: space-around;
        align-items: center;
        background-color: #1B5E20;
        padding: 10px 0;
        position: fixed;
        top: 0;
        width: 100%;
        z-index: 1000;
    }
    
    header div {
        font-size: 1.2em;
    }
    
    #sound-toggle {
        cursor: pointer;
        font-size: 1.5em;
    }
    
    main {
        display: flex;
        justify-content: center;
        align-items: flex-start;
        padding-top: 70px; /* 预留头部空间 */
        padding-bottom: 20px;
    }
    
    .card-frame {
        background-color: #388E3C;
        border: 2px dashed #81C784;
        border-radius: 10px;
        width: 300px;
        min-height: 400px;
        padding: 20px;
        box-sizing: border-box;
        position: relative;
    }
    
    .frame-title {
        text-align: center;
        margin-bottom: 10px;
        font-weight: bold;
    }
    
    .deck, .target {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        min-height: 300px;
    }
    
    .deck .card {
        width: 60px;
        height: 90px;
        background-color: #FFFFFF;
        border: 1px solid #000000;
        border-radius: 5px;
        margin: 5px;
        cursor: grab;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 1.2em;
        position: relative;
    }
    
    .target .card-slot {
        width: 60px;
        height: 90px;
        border: 2px dashed #FFFFFF;
        border-radius: 5px;
        margin: 5px;
        position: relative;
    }
    
    .tooltip {
        position: absolute;
        bottom: 10px;
        left: 50%;
        transform: translateX(-50%);
        background-color: rgba(0,0,0,0.7);
        color: #FFFFFF;
        padding: 5px 10px;
        border-radius: 5px;
        font-size: 0.9em;
        display: none;
    }
    
    .card-frame:hover .tooltip {
        display: block;
    }
    
    .spacer {
        width: 50px;
    }
    
    .modal {
        position: fixed;
        top: 0;
        left: 0;
        right:0;
        bottom:0;
        background-color: rgba(0,0,0,0.8);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 2000;
    }
    
    .modal.hidden {
        display: none;
    }
    
    .modal-content {
        background-color: #FFFFFF;
        color: #000000;
        padding: 20px 40px;
        border-radius: 10px;
        text-align: center;
    }
    
    .modal-content h2 {
        margin-top: 0;
    }
    
    button {
        padding: 10px 20px;
        margin-top: 15px;
        font-size: 1em;
        cursor: pointer;
        border: none;
        border-radius: 5px;
        background-color: #1B5E20;
        color: #FFFFFF;
        transition: background-color 0.3s;
    }
    
    button:hover {
        background-color: #2E7D32;
    }
    
    .message-box {
        position: fixed;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
        background-color: rgba(0,0,0,0.7);
        color: #FFFFFF;
        padding: 10px 20px;
        border-radius: 5px;
        font-size: 1em;
        z-index: 1500;
    }
    
    .message-box.hidden {
        display: none;
    }
    \end{lstlisting}
\end{enumerate}

\subsection{编写JavaScript逻辑}

\begin{enumerate}[resume]
    \item \textbf{变量和元素引用}：
    \begin{itemize}
        \item 获取页面中各个需要操作的DOM元素，如扑克牌区域、目标区域、计分板、计时器等。
        \item 初始化游戏变量，如扑克牌组、拖动次数、得分、剩余时间等。
    \end{itemize}
    
    \item \textbf{游戏初始化 (\texttt{initGame})}：
    \begin{itemize}
        \item 生成一副扑克牌，包括四种花色和13个点数。
        \item 使用洗牌算法（如Fisher-Yates算法）打乱扑克牌的顺序。
        \item 将扑克牌动态渲染到A框中，设置每张牌的拖放属性。
        \item 重置计分板、计时器和游戏状态，确保游戏重新开始时一切正常。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=游戏初始化函数 initGame]
    function initGame() {
        deck = generateDeck();
        shuffleDeck(deck);
        renderDeck();
        dragCount = 0;
        score = 0;
        timeLeft = 60;
        updateStats();
        clearInterval(timerInterval);
        timerInterval = setInterval(updateTimer, 1000);
        endgameModal.classList.add('hidden');
        messageBox.classList.add('hidden');
        clearTarget();
    }
    \end{lstlisting}
    
    \item \textbf{拖放功能实现}：
    \begin{itemize}
        \item 为每张扑克牌添加拖动事件监听器（\texttt{dragstart}和\texttt{dragend}）。
        \item 为目标区域的卡槽添加拖放事件监听器（\texttt{dragover}和\texttt{drop}）。
        \item 在拖放过程中，控制扑克牌的显示和隐藏，确保用户体验流畅。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=添加拖放事件监听器]
    function addDragEventListeners(card) {
        card.addEventListener('dragstart', handleDragStart);
        card.addEventListener('dragend', handleDragEnd);
    }
    
    let draggedCard = null;
    
    function handleDragStart(e) {
        draggedCard = this;
        setTimeout(() => {
            this.style.display = 'none';
        }, 0);
    }
    
    function handleDragEnd(e) {
        this.style.display = 'flex';
        draggedCard = null;
    }
    
    const slots = targetElement.querySelectorAll('.card-slot');
    slots.forEach(slot => {
        slot.addEventListener('dragover', handleDragOver);
        slot.addEventListener('drop', handleDrop);
    });
    
    function handleDragOver(e) {
        e.preventDefault();
    }
    
    function handleDrop(e) {
        e.preventDefault();
        if (!draggedCard) return;
        const slot = this;
        if (slot.children.length > 0) {
            showMessage('这个卡槽已经有牌了！');
            if (soundOn) failSound.play();
            return;
        }
        slot.appendChild(draggedCard);
        draggedCard.style.position = 'relative';
        draggedCard.style.left = '0';
        draggedCard.style.top = '0';
        dragCount++;
        updateStats();
        checkIfTargetComplete();
    }
    \end{lstlisting}
    
    \item \textbf{顺子验证}：
    \begin{itemize}
        \item 当玩家将5张扑克牌拖放到B框的卡槽中时，收集这些扑克牌的点数。
        \item 将点数排序后，检查是否为连续递增的序列（顺子）。
        \item 根据验证结果更新得分，并给予用户相应的提示和音效反馈。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=顺子验证函数 checkIfTargetComplete]
    function checkIfTargetComplete() {
        const placedCards = Array.from(targetElement.querySelectorAll('.card'));
        if (placedCards.length === 5) {
            const cardValues = placedCards.map(card => getCardValue(card.textContent));
            cardValues.sort((a, b) => a - b);
            if (isStraight(cardValues)) {
                score += 10;
                updateStats();
                if (soundOn) successSound.play();
                showMessage('恭喜！你完成了一个顺子！');
                clearTarget();
            } else {
                score -= 5;
                updateStats();
                if (soundOn) failSound.play();
                showMessage('这不是一个顺子，请再试一次！');
                clearTarget();
            }
        }
    }
    \end{lstlisting}
    
    \item \textbf{计时器功能}：
    \begin{itemize}
        \item 启动一个倒计时计时器，限制游戏时间为60秒。
        \item 每秒更新一次计时器显示，当时间耗尽时，结束游戏并显示最终得分。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=计时器函数 updateTimer]
    function updateTimer() {
        timeLeft--;
        timerElement.textContent = `时间：${timeLeft}秒`;
        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            endGame();
        }
    }
    
    function endGame() {
        finalCount.textContent = `你的拖动次数是 ${dragCount} 次，得分：${score} 分！`;
        endgameModal.classList.remove('hidden');
    }
    \end{lstlisting}
    
    \item \textbf{音效控制}：
    \begin{itemize}
        \item 提供音效开关按钮，允许用户静音或开启游戏音效。
        \item 根据用户选择，控制成功和失败音效的播放。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=音效控制代码]
    soundToggle.addEventListener('click', () => {
        soundOn = !soundOn;
        soundToggle.textContent = soundOn ? '🔊' : '🔇';
    });
    \end{lstlisting}
    
    \item \textbf{用户交互功能}：
    \begin{itemize}
        \item 实现重置游戏和重玩的功能，允许用户在游戏结束后重新开始。
        \item 显示临时的消息提示框，向用户反馈操作结果。
    \end{itemize}
    
    \begin{lstlisting}[language=JavaScript, caption=重置和重玩按钮事件监听器]
    replayButton.addEventListener('click', () => {
        initGame();
    });
    
    resetButton.addEventListener('click', () => {
        initGame();
    });
    
    function showMessage(msg) {
        messageBox.textContent = msg;
        messageBox.classList.remove('hidden');
        setTimeout(() => {
            messageBox.classList.add('hidden');
        }, 2000);
    }
    \end{lstlisting}
    
    \item \textbf{代码优化和模块化}：
    \begin{itemize}
        \item 将不同功能模块化，便于代码的维护和扩展。
        \item 添加充分的注释，解释每个功能模块的作用和实现方法。
    \end{itemize}
\end{enumerate}

\end{document}
```

**说明：**

1. **代码环境设置**：
    - 确保在LaTeX文档的导言区（`\documentclass`之后，`\begin{document}`之前）引入了必要的包，如`listings`和`xcolor`，用于代码高亮显示和颜色设置。
    - 代码块使用`lstlisting`环境，并通过`language=HTML`, `language=CSS`, `language=JavaScript`指定代码类型，以实现相应的语法高亮。

2. **代码插入**：
    - 关键的HTML、CSS和JavaScript代码段已被插入到相应的小节中，使用`\begin{lstlisting}`和`\end{lstlisting}`包裹，并添加了`caption`参数以提供代码的标题和说明。

3. **保持原文不变**：
    - 原有的实验步骤描述和内容被保留，关键代码段被整齐地嵌入到相关部分，增强了报告的可读性和专业性。

4. **图片替换**：
    - 用户提到会进行图片替换，因此在相关部分未插入图片，或可插入占位符图像以便后续替换。例如，可以使用`\includegraphics`命令插入图像，并在需要时替换为实际的截图。

5. **示例代码**：
    - 根据用户提供的代码，关键部分如项目初始化、拖放功能实现、顺子验证、计时器功能、音效控制以及用户交互功能等均已插入对应的JavaScript代码块。

6. **进一步优化**：
    - 可以根据需要调整`lstset`参数，以改变代码的显示样式，如字体大小、颜色、行号等。
    - 确保所有代码文件（如`scripts.js`、`styles.css`等）与报告中的代码保持同步，避免不一致。

**最终效果：**

上述LaTeX代码将生成一个结构清晰、内容丰富的实验步骤章节，详细描述了扑克牌顺子游戏的开发过程，并通过代码示例展示了关键实现部分，使读者能够更好地理解项目的技术细节。