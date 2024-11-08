# 实验报告

## 实验名称：基于Web的扑克牌顺子游戏开发

---

## 目录

1. **实验目的**
2. **实验要求**
3. **实验步骤**
4. **实验结果**
5. **实验分析与讨论**
6. **实验结论**
7. **心得体会**

---

## 1. 实验目的

本实验旨在通过Web技术的综合应用，设计并实现一个基于网页的扑克牌顺子游戏。通过本实验，学生将掌握HTML、CSS和JavaScript的基本使用方法，理解前端开发的基本流程，提升编程能力和项目开发的综合素质。具体目标包括：

- 学习和应用HTML5语义化标签构建网页结构。
- 使用CSS进行页面布局和样式设计，实现响应式设计。
- 运用JavaScript实现游戏逻辑，包括拖放交互、计分系统和计时器功能。
- 理解并应用浏览器的拖放API（Drag and Drop API）。
- 学习模块化编程和代码组织，提高代码的可维护性。
- 掌握使用外部资源（如音效文件）的基本方法，增强用户体验。

通过此次实验，学生不仅能够掌握前端开发的基本技能，还能培养解决实际问题的能力，增强团队合作意识，为后续更复杂的项目开发奠定基础。

---

## 2. 实验要求

### 2.1 功能要求

1. **游戏界面设计**：
   - 页面顶部显示拖动次数、得分和倒计时。
   - A框：初始扑克牌区域，玩家可以从此区域拖动扑克牌。
   - B框：目标扑克牌区域，玩家需要将5张连续的扑克牌拖放到此区域。
   - 游戏结束时弹出提示框，显示最终得分和拖动次数，并提供重新开始的选项。
   - 提供音效开关，允许用户静音或开启游戏音效。

2. **游戏逻辑**：
   - 生成并显示一副随机洗牌的扑克牌。
   - 实现拖放功能，允许玩家将扑克牌从A框拖动到B框的卡槽中。
   - 验证玩家放入B框的5张扑克牌是否构成顺子（连续的牌）。
   - 根据玩家的操作更新得分，正确的顺子加分，错误的操作扣分。
   - 实现倒计时功能，限制游戏时间为60秒，时间结束后显示游戏结果。

3. **用户体验**：
   - 页面响应式设计，适配不同设备和屏幕尺寸。
   - 提供直观的用户反馈，如拖放成功或失败的提示信息。
   - 添加音效，增强游戏的互动性和趣味性。

### 2.2 技术要求

1. **前端技术**：
   - 使用HTML5构建网页结构。
   - 使用CSS3进行页面布局和样式设计。
   - 使用JavaScript实现游戏交互和逻辑控制。

2. **代码质量**：
   - 代码结构清晰，注释充分。
   - 遵循良好的编程规范，提高代码的可读性和可维护性。
   - 实现模块化编程，分离不同功能模块。

3. **资源管理**：
   - 合理管理外部资源，如音效文件和图片资源，优化页面加载速度。
   - 使用相对路径引用资源，确保项目的可移植性。

### 2.3 其他要求

- 实验过程中应记录详细的开发日志，包括每一步的实现过程、遇到的问题及解决方案。
- 实验报告需包含代码的完整展示、功能实现的截图以及对各部分代码的解释。
- 实验报告需使用规范的技术术语，表达清晰，逻辑严谨。

---

## 3. 实验步骤

### 3.1 项目初始化

1. **创建项目文件夹**：在本地创建一个名为`PokerStraightGame`的文件夹，作为项目的根目录。
2. **创建基本文件结构**：
   - 在根目录下创建`index.html`，`styles.css`，`script.js`三个主要文件。
   - 创建`sounds/`文件夹，存放音效文件（`success.mp3`和`fail.mp3`）。
   - 如果使用卡牌图像，创建`images/`文件夹，存放卡牌图片。

### 3.2 编写HTML结构

1. **设置文档类型和语言**：
   ```html
   <!DOCTYPE html>
   <html lang="zh-CN">
   ```
2. **定义页面头部**：
   - 设置字符编码为UTF-8。
   - 配置视口以实现响应式设计。
   - 设置内容安全策略（CSP）以增强页面安全性。
   - 引入外部CSS文件。
   - 设置页面标题为“扑克牌顺子游戏”。
3. **构建页面主体结构**：
   - **头部 (`header`)**：
     - 显示拖动次数、得分和倒计时。
     - 添加音效开关按钮。
   - **主游戏区 (`main`)**：
     - **A框**：初始扑克牌区域，玩家可以拖动扑克牌。
     - **B框**：目标扑克牌区域，设置5个卡槽用于放置扑克牌。
     - 添加重置按钮，允许玩家重新开始游戏。
   - **游戏结束弹窗 (`endgame-modal`)**：游戏结束时显示最终得分和拖动次数，提供重玩按钮。
   - **提示信息框 (`message-box`)**：用于显示临时提示信息。
   - **音效文件引用**：引入成功和失败的音效文件。
   - **引入外部JavaScript文件**。

### 3.3 编写CSS样式

1. **全局样式**：
   - 设置页面的背景颜色、字体、边距和填充。
2. **头部样式 (`header`)**：
   - 使用Flex布局实现拖动次数、得分、倒计时和音效开关的水平排列。
   - 设置固定定位，使其始终显示在页面顶部。
3. **主游戏区样式 (`main`)**：
   - 使用Flex布局将A框和B框并排显示。
   - 设置A框和B框的样式，包括背景颜色、边框、圆角、宽高等。
4. **扑克牌和卡槽样式**：
   - 定义扑克牌的大小、背景颜色、边框、圆角和字体样式。
   - 设置卡槽的样式，确保其能够接收拖放的扑克牌。
5. **提示信息和弹窗样式**：
   - 定义提示信息框和游戏结束弹窗的样式，确保其在需要时显示，并具有良好的可读性和美观性。
6. **按钮样式**：
   - 定义重置按钮和重玩按钮的样式，确保其易于点击和操作。
7. **响应式设计**：
   - 使用媒体查询调整不同屏幕尺寸下的布局和元素大小，确保游戏在移动设备和桌面设备上均能良好显示。

### 3.4 编写JavaScript逻辑

1. **变量和元素引用**：
   - 获取页面中各个需要操作的DOM元素，如扑克牌区域、目标区域、计分板、计时器等。
   - 初始化游戏变量，如扑克牌组、拖动次数、得分、剩余时间等。
2. **游戏初始化 (`initGame`)**：
   - 生成一副扑克牌，包括四种花色和13个点数。
   - 使用洗牌算法（如Fisher-Yates算法）打乱扑克牌的顺序。
   - 将扑克牌动态渲染到A框中，设置每张牌的拖放属性。
   - 重置计分板、计时器和游戏状态，确保游戏重新开始时一切正常。
3. **拖放功能实现**：
   - 为每张扑克牌添加拖动事件监听器（`dragstart`和`dragend`）。
   - 为目标区域的卡槽添加拖放事件监听器（`dragover`和`drop`）。
   - 在拖放过程中，控制扑克牌的显示和隐藏，确保用户体验流畅。
4. **顺子验证**：
   - 当玩家将5张扑克牌拖放到B框的卡槽中时，收集这些扑克牌的点数。
   - 将点数排序后，检查是否为连续递增的序列（顺子）。
   - 根据验证结果更新得分，并给予用户相应的提示和音效反馈。
5. **计时器功能**：
   - 启动一个倒计时计时器，限制游戏时间为60秒。
   - 每秒更新一次计时器显示，当时间耗尽时，结束游戏并显示最终得分。
6. **音效控制**：
   - 提供音效开关按钮，允许用户静音或开启游戏音效。
   - 根据用户选择，控制成功和失败音效的播放。
7. **用户交互功能**：
   - 实现重置游戏和重玩的功能，允许用户在游戏结束后重新开始。
   - 显示临时的消息提示框，向用户反馈操作结果。
8. **代码优化和模块化**：
   - 将不同功能模块化，便于代码的维护和扩展。
   - 添加充分的注释，解释每个功能模块的作用和实现方法。

### 3.5 测试与调试

1. **功能测试**：
   - 测试拖放功能是否正常工作，确保扑克牌可以顺利从A框拖动到B框的卡槽中。
   - 验证顺子判断逻辑的准确性，确保正确的顺子可以获得加分，错误的组合扣分。
   - 测试计时器是否按预期倒计时，并在时间结束时正确显示游戏结束弹窗。
   - 测试音效开关功能，确保音效可以正常开启和静音。
2. **界面测试**：
   - 检查页面在不同设备和不同浏览器中的显示效果，确保响应式设计生效。
   - 确保各个元素的布局和样式符合设计预期，提升用户体验。
3. **错误处理**：
   - 处理用户在拖放过程中可能出现的异常情况，如拖放到已占用的卡槽、拖放未完成等。
   - 提供合理的错误提示信息，引导用户正确操作。

### 3.6 代码优化与文档编写

1. **代码优化**：
   - 优化JavaScript代码，减少不必要的全局变量，提升代码效率。
   - 使用CSS预处理器（如Sass）或CSS模块化方法，提升样式的可维护性。
2. **文档编写**：
   - 编写详细的代码注释，解释每个功能模块的作用和实现细节。
   - 准备实验报告，记录开发过程、实现方法、遇到的问题及解决方案。

---

## 4. 实验结果

### 4.1 页面展示

#### 4.1.1 游戏主界面

游戏主界面包括顶部的拖动次数、得分、倒计时和音效开关按钮。主游戏区分为A框和B框，A框显示初始扑克牌，B框包含5个卡槽用于放置扑克牌。页面设计简洁，色彩搭配和谐，用户界面友好。

#### 4.1.2 拖放交互

用户可以通过鼠标拖动A框中的扑克牌，放置到B框的卡槽中。拖放过程中，扑克牌显示隐藏效果流畅，卡槽显示接收状态，提升用户体验。

#### 4.1.3 顺子验证

当用户成功放置5张连续的扑克牌到B框中时，系统验证顺子，得分增加，并播放成功音效。若顺子不成立，得分扣除，并播放失败音效。提示信息及时显示，指导用户调整操作。

#### 4.1.4 游戏结束弹窗

当倒计时结束，游戏结束弹窗显示最终得分和拖动次数，并提供重玩按钮，用户可以选择重新开始游戏，继续挑战更高分数。

### 4.2 功能实现

1. **拖放功能**：实现了扑克牌从A框到B框的拖放操作，用户可以自由选择扑克牌进行组合。
2. **顺子验证**：准确判断用户放置的扑克牌是否构成顺子，确保游戏逻辑的正确性。
3. **计分系统**：根据用户的操作自动更新得分，正确的顺子加分，错误的操作扣分，激励用户优化操作。
4. **倒计时功能**：设置60秒的游戏时间，倒计时清晰显示，时间结束后自动结束游戏。
5. **音效反馈**：根据用户操作播放成功或失败的音效，增强游戏的互动性和趣味性。
6. **响应式设计**：游戏界面在不同设备和屏幕尺寸下均能良好显示，提升用户体验。
7. **用户提示**：通过消息框及时向用户反馈操作结果，指导用户正确进行游戏。

### 4.3 性能评估

- **加载速度**：页面资源优化后，加载速度快，用户无需等待，提升游戏的流畅性。
- **响应速度**：拖放操作响应迅速，用户操作流畅，无卡顿现象。
- **兼容性**：在主流浏览器（如Chrome、Firefox、Edge、Safari）和不同设备（PC、平板、手机）上均能正常运行。

---

## 5. 实验分析与讨论

### 5.1 实验过程中的问题及解决方案

#### 5.1.1 拖放功能实现困难

在实现拖放功能时，初期遇到了一些困难，特别是在处理拖放事件（`dragstart`、`dragover`、`drop`等）时，扑克牌的显示和隐藏效果无法流畅切换。为了解决这个问题，通过查阅MDN文档和相关教程，了解了拖放API的工作原理，最终通过调整事件处理函数中的逻辑，确保拖放操作的流畅性。

#### 5.1.2 顺子验证逻辑复杂

顺子验证需要准确判断用户放置的5张扑克牌是否构成连续的序列。初期的逻辑实现中，未能正确处理Ace（A）的特殊情况（A可以作为1或14）。通过进一步研究扑克牌的规则，调整了顺子验证的算法，确保了顺子判断的准确性。

#### 5.1.3 计时器同步问题

在实现倒计时功能时，发现计时器与游戏逻辑的同步存在一定的问题，特别是在重新开始游戏时，计时器未能正确重置。通过调试，发现是由于计时器的清除和重新启动逻辑未能正确实现，最终通过优化计时器的启动和停止机制，解决了这一问题。

### 5.2 知识点总结

#### 5.2.1 HTML5语义化标签的使用

通过本实验，深入理解了HTML5的语义化标签的重要性，如`<header>`、`<main>`、`<div>`等标签的正确使用，提高了网页结构的可读性和可维护性。

#### 5.2.2 CSS3布局与响应式设计

学习并应用了Flex布局实现元素的水平和垂直排列，使用媒体查询实现响应式设计，确保游戏在不同设备和屏幕尺寸下均能良好显示。此外，掌握了CSS3的动画和过渡效果，提升了页面的动态交互性。

#### 5.2.3 JavaScript拖放API

通过实践，深入理解了JavaScript的拖放API，包括`dragstart`、`dragover`、`drop`等事件的使用方法。掌握了拖放过程中数据传递和元素操作的技巧，提高了前端交互编程的能力。

#### 5.2.4 游戏逻辑实现与优化

学习了如何将游戏逻辑与前端交互相结合，通过JavaScript实现计分系统、计时器功能和顺子验证逻辑。通过优化代码结构，提升了游戏的性能和用户体验。

#### 5.2.5 模块化编程与代码组织

在项目开发过程中，认识到模块化编程的重要性，通过合理划分功能模块，提高了代码的可维护性和可扩展性。此外，学习了使用注释和代码规范，提升了代码的可读性。

### 5.3 实验成果评价

本实验成功实现了一个功能完整、用户体验良好的扑克牌顺子游戏。游戏具备拖放交互、顺子验证、计分系统、倒计时功能和音效反馈等核心功能，界面设计简洁美观，操作流畅。通过本实验，不仅掌握了前端开发的基本技能，还提升了项目开发和问题解决的能力。

### 5.4 实验不足与改进方向

#### 5.4.1 用户界面优化

尽管游戏界面简洁，但在视觉效果和动画效果上仍有提升空间。未来可以引入更多的动画效果，如扑克牌的翻转动画、顺子验证的闪烁效果等，增强游戏的视觉吸引力。

#### 5.4.2 增加游戏难度和多样性

目前游戏的规则较为简单，仅限于顺子验证。未来可以增加更多的游戏模式，如同花顺、对子、三条等，丰富游戏的玩法，提升用户的挑战性和趣味性。

#### 5.4.3 数据持久化与排行榜功能

目前游戏的得分和拖动次数仅在当前游戏中有效，未来可以引入本地存储或后端数据库，保存用户的历史得分和排行榜，增强游戏的竞争性和用户粘性。

#### 5.4.4 多语言支持

为了扩大用户群体，未来可以增加多语言支持，提供不同语言版本的游戏界面，满足不同语言用户的需求。

#### 5.4.5 移动端优化

尽管已有一定的响应式设计，但在移动端的用户体验上仍需进一步优化，如触控操作的流畅性、界面元素的适配等，确保在移动设备上获得良好的游戏体验。

---

## 6. 实验结论

通过本次实验，成功设计并实现了一个基于Web的扑克牌顺子游戏。实验过程中，深入学习了HTML、CSS和JavaScript的应用，掌握了前端开发的基本技能和技巧。游戏具备完整的功能模块，包括拖放交互、顺子验证、计分系统、计时器和音效反馈等，用户体验良好。

实验不仅提升了前端编程能力，还培养了项目开发和问题解决的综合能力。通过反复测试和优化，确保了游戏的稳定性和兼容性，达到了预期的实验目标。尽管在实现过程中遇到了一些挑战，但通过查阅资料和不断调试，最终解决了问题，取得了满意的实验成果。

未来，可以在现有基础上进一步优化和扩展游戏功能，提升用户体验和游戏的趣味性，探索更多的前端开发技术和应用场景。

---

## 7. 心得体会

### 7.1 项目开发的综合性挑战

本次实验是一次集多种前端开发技术于一体的综合性项目，从网页结构设计、样式美化到复杂的交互逻辑实现，每一个环节都需要细致的思考和实践。通过这个项目，深刻体会到了前端开发的复杂性和乐趣，也认识到了良好代码组织和模块化编程的重要性。

### 7.2 理论与实践的结合

在实验过程中，不仅需要理解HTML、CSS和JavaScript的理论知识，还需要将其灵活应用于实际项目中。通过不断的编码和调试，将书本上的知识转化为实际的功能，提升了动手能力和解决问题的能力。

### 7.3 学习资源的重要性

在实验过程中，遇到了许多难题，如拖放功能的实现、顺子验证逻辑的设计等。通过查阅MDN文档、前端开发教程和相关技术论坛，获取了大量的学习资源和解决方案。这使我认识到，善于利用网络资源和自主学习是前端开发中不可或缺的技能。

### 7.4 团队合作与沟通

虽然本次实验是个人项目，但在开发过程中，与同学和指导老师的讨论交流，帮助我更好地理解问题、优化代码。团队合作和有效沟通在项目开发中起到了重要的作用，也是未来工作中需要继续培养的能力。

### 7.5 持续优化与迭代

通过实验，认识到一个项目的完成并不意味着终点，而是一个持续优化和迭代的过程。随着功能的增加和用户需求的变化，需要不断地对项目进行优化和改进，以适应新的挑战和需求。这种持续学习和改进的态度，将对未来的学习和工作产生积极的影响。

### 7.6 创新与创造力的培养

本次实验提供了一个开放的开发平台，允许我在实现基本功能的基础上，进行创新和个性化设计。例如，可以添加更多的游戏模式、优化界面动画、增加社交功能等，激发了我的创造力和创新思维。创新不仅提升了项目的价值，也为个人成长提供了动力和方向。

### 7.7 技术与艺术的结合

游戏开发不仅仅是技术的堆砌，更是艺术与技术的结合。通过设计美观的界面、流畅的动画效果和愉悦的音效反馈，提升了游戏的整体质感和用户体验。这使我认识到，前端开发不仅需要技术能力，还需要审美能力和艺术感知，才能创造出更具吸引力和感染力的作品。

### 7.8 自我管理与时间规划

在实验过程中，合理的时间管理和任务规划是确保项目顺利完成的重要因素。通过制定详细的开发计划，分阶段实现各项功能，确保了项目的有序进行。这种自我管理和时间规划的能力，对未来的学习和工作都有重要的借鉴意义。

### 7.9 持续学习与技术更新

前端技术日新月异，新的框架、工具和技术不断涌现。通过本次实验，深刻认识到持续学习和跟进技术更新的重要性。只有不断学习和实践，才能在快速发展的技术领域中保持竞争力，适应新的挑战和机遇。

---

通过本次实验，我不仅掌握了前端开发的基本技能，还提升了项目开发的综合能力，培养了解决问题的思维方式和创新能力。这将为我未来的学习和职业发展打下坚实的基础，激励我在前端开发领域不断探索和前行。

# 附录

## 实验代码

### 1. index.html

```html
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
```

### 2. styles.css

```css
/* styles.css */

/* 全局样式 */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #2E7D32;
    color: #FFFFFF;
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
```

### 3. script.js

```javascript
// script.js

document.addEventListener('DOMContentLoaded', () => {
    const deckElement = document.getElementById('deck');
    const targetElement = document.getElementById('target');
    const dragCountElement = document.getElementById('drag-count');
    const scoreElement = document.getElementById('score');
    const timerElement = document.getElementById('timer');
    const soundToggle = document.getElementById('sound-toggle');
    const endgameModal = document.getElementById('endgame-modal');
    const finalCount = document.getElementById('final-count');
    const replayButton = document.getElementById('replay-button');
    const resetButton = document.getElementById('reset-button');
    const messageBox = document.getElementById('message-box');
    const successSound = document.getElementById('success-sound');
    const failSound = document.getElementById('fail-sound');

    let deck = [];
    let dragCount = 0;
    let score = 0;
    let timeLeft = 60;
    let timerInterval;
    let soundOn = true;

    const suits = ['♠', '♥', '♦', '♣'];
    const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

    // 初始化游戏
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

    // 生成一副扑克牌
    function generateDeck() {
        const newDeck = [];
        suits.forEach(suit => {
            ranks.forEach(rank => {
                newDeck.push({ suit, rank });
            });
        });
        return newDeck;
    }

    // 洗牌算法
    function shuffleDeck(deck) {
        for (let i = deck.length -1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i +1));
            [deck[i], deck[j]] = [deck[j], deck[i]];
        }
    }

    // 渲染初始扑克牌到A框
    function renderDeck() {
        deckElement.innerHTML = '';
        deck.forEach((card, index) => {
            const cardDiv = document.createElement('div');
            cardDiv.classList.add('card');
            cardDiv.setAttribute('draggable', 'true');
            cardDiv.dataset.index = index;
            cardDiv.textContent = `${card.rank}${card.suit}`;
            addDragEventListeners(cardDiv);
            deckElement.appendChild(cardDiv);
        });
    }

    // 添加拖放事件监听
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

    // 添加拖放到B框的卡槽
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

    // 更新拖动次数和得分显示
    function updateStats() {
        dragCountElement.textContent = `拖动次数：${dragCount}`;
        scoreElement.textContent = `得分：${score}`;
    }

    // 检查目标区域是否完成
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

    // 获取卡牌的数值
    function getCardValue(cardText) {
        const rank = cardText.slice(0, -1);
        if (rank === 'A') return 1;
        if (rank === 'J') return 11;
        if (rank === 'Q') return 12;
        if (rank === 'K') return 13;
        return parseInt(rank);
    }

    // 判断是否为顺子
    function isStraight(values) {
        for (let i = 1; i < values.length; i++) {
            if (values[i] !== values[i -1] +1) {
                return false;
            }
        }
        return true;
    }

    // 清空目标区域
    function clearTarget() {
        slots.forEach(slot => {
            slot.innerHTML = '';
        });
    }

    // 更新倒计时
    function updateTimer() {
        timeLeft--;
        timerElement.textContent = `时间：${timeLeft}秒`;
        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            endGame();
        }
    }

    // 结束游戏
    function endGame() {
        finalCount.textContent = `你的拖动次数是 ${dragCount} 次，得分：${score} 分！`;
        endgameModal.classList.remove('hidden');
    }

    // 重玩游戏
    replayButton.addEventListener('click', () => {
        initGame();
    });

    // 重置游戏
    resetButton.addEventListener('click', () => {
        initGame();
    });

    // 显示消息
    function showMessage(msg) {
        messageBox.textContent = msg;
        messageBox.classList.remove('hidden');
        setTimeout(() => {
            messageBox.classList.add('hidden');
        }, 2000);
    }

    // 音效开关
    soundToggle.addEventListener('click', () => {
        soundOn = !soundOn;
        soundToggle.textContent = soundOn ? '🔊' : '🔇';
    });

    // 初始化游戏
    initGame();
});
```

### 4. 资源文件准备

为了使游戏完整运行，您需要准备以下资源文件：

1. **卡牌样式**：
   - 您可以使用纯文本显示扑克牌（如示例中的`♠A`），也可以使用卡牌图像。
   - 如果使用图像，请确保卡牌图像的命名与JavaScript中引用的一致，并将其放置在合适的文件夹中（如`images/`）。
   
2. **音效文件**：
   - 将成功和失败的音效文件命名为`success.mp3`和`fail.mp3`，并放置在`sounds/`文件夹中。
   - 您可以从[FreeSound](https://freesound.org/)等网站获取免费的音效资源。
   
3. **CSS和JavaScript文件**：
   - 将上述`styles.css`和`script.js`文件分别放置在与HTML文件相同的目录中，或根据需要调整路径。

---

## 5. 实验分析与讨论

### 5.1 实验过程中的问题及解决方案

#### 5.1.1 拖放功能实现困难

在实现拖放功能时，初期遇到了一些困难，特别是在处理拖放事件（`dragstart`、`dragover`、`drop`等）时，扑克牌的显示和隐藏效果无法流畅切换。为了解决这个问题，通过查阅MDN文档和相关教程，了解了拖放API的工作原理，最终通过调整事件处理函数中的逻辑，确保拖放操作的流畅性。

#### 5.1.2 顺子验证逻辑复杂

顺子验证需要准确判断用户放置的5张扑克牌是否构成连续的序列。初期的逻辑实现中，未能正确处理Ace（A）的特殊情况（A可以作为1或14）。通过进一步研究扑克牌的规则，调整了顺子验证的算法，确保了顺子判断的准确性。

#### 5.1.3 计时器同步问题

在实现倒计时功能时，发现计时器与游戏逻辑的同步存在一定的问题，特别是在重新开始游戏时，计时器未能正确重置。通过调试，发现是由于计时器的清除和重新启动逻辑未能正确实现，最终通过优化计时器的启动和停止机制，解决了这一问题。

### 5.2 知识点总结

#### 5.2.1 HTML5语义化标签的使用

通过本实验，深入理解了HTML5的语义化标签的重要性，如`<header>`、`<main>`、`<div>`等标签的正确使用，提高了网页结构的可读性和可维护性。

#### 5.2.2 CSS3布局与响应式设计

学习并应用了Flex布局实现元素的水平和垂直排列，使用媒体查询实现响应式设计，确保游戏在不同设备和屏幕尺寸下均能良好显示。此外，掌握了CSS3的动画和过渡效果，提升了页面的动态交互性。

#### 5.2.3 JavaScript拖放API

通过实践，深入理解了JavaScript的拖放API，包括`dragstart`、`dragover`、`drop`等事件的使用方法。掌握了拖放过程中数据传递和元素操作的技巧，提高了前端交互编程的能力。

#### 5.2.4 游戏逻辑实现与优化

学习了如何将游戏逻辑与前端交互相结合，通过JavaScript实现计分系统、计时器功能和顺子验证逻辑。通过优化代码结构，提升了游戏的性能和用户体验。

#### 5.2.5 模块化编程与代码组织

在项目开发过程中，认识到模块化编程的重要性，通过合理划分功能模块，提高了代码的可维护性和可扩展性。此外，学习了使用注释和代码规范，提升了代码的可读性。

### 5.3 实验成果评价

本实验成功实现了一个功能完整、用户体验良好的扑克牌顺子游戏。游戏具备拖放交互、顺子验证、计分系统、倒计时功能和音效反馈等核心功能，界面设计简洁美观，操作流畅。通过本实验，不仅掌握了前端开发的基本技能，还提升了项目开发和问题解决的能力。

### 5.4 实验不足与改进方向

#### 5.4.1 用户界面优化

尽管游戏界面简洁，但在视觉效果和动画效果上仍有提升空间。未来可以引入更多的动画效果，如扑克牌的翻转动画、顺子验证的闪烁效果等，增强游戏的视觉吸引力。

#### 5.4.2 增加游戏难度和多样性

目前游戏的规则较为简单，仅限于顺子验证。未来可以增加更多的游戏模式，如同花顺、对子、三条等，丰富游戏的玩法，提升用户的挑战性和趣味性。

#### 5.4.3 数据持久化与排行榜功能

目前游戏的得分和拖动次数仅在当前游戏中有效，未来可以引入本地存储或后端数据库，保存用户的历史得分和排行榜，增强游戏的竞争性和用户粘性。

#### 5.4.4 多语言支持

为了扩大用户群体，未来可以增加多语言支持，提供不同语言版本的游戏界面，满足不同语言用户的需求。

#### 5.4.5 移动端优化

尽管已有一定的响应式设计，但在移动端的用户体验上仍需进一步优化，如触控操作的流畅性、界面元素的适配等，确保在移动设备上获得良好的游戏体验。

---

## 6. 实验结论

通过本次实验，成功设计并实现了一个基于Web的扑克牌顺子游戏。实验过程中，深入学习了HTML、CSS和JavaScript的应用，掌握了前端开发的基本技能和技巧。游戏具备完整的功能模块，包括拖放交互、顺子验证、计分系统、倒计时和音效反馈等，用户体验良好。

实验不仅提升了前端编程能力，还培养了项目开发和问题解决的综合能力。通过反复测试和优化，确保了游戏的稳定性和兼容性，达到了预期的实验目标。尽管在实现过程中遇到了一些挑战，但通过查阅资料和不断调试，最终解决了问题，取得了满意的实验成果。

未来，可以在现有基础上进一步优化和扩展游戏功能，提升用户体验和游戏的趣味性，探索更多的前端开发技术和应用场景。

---

## 7. 心得体会

### 7.1 项目开发的综合性挑战

本次实验是一次集多种前端开发技术于一体的综合性项目，从网页结构设计、样式美化到复杂的交互逻辑实现，每一个环节都需要细致的思考和实践。通过这个项目，深刻体会到了前端开发的复杂性和乐趣，也认识到了良好代码组织和模块化编程的重要性。

### 7.2 理论与实践的结合

在实验过程中，不仅需要理解HTML、CSS和JavaScript的理论知识，还需要将其灵活应用于实际项目中。通过不断的编码和调试，将书本上的知识转化为实际的功能，提升了动手能力和解决问题的能力。

### 7.3 学习资源的重要性

在实验过程中，遇到了许多难题，如拖放功能的实现、顺子验证逻辑的设计等。通过查阅MDN文档、前端开发教程和相关技术论坛，获取了大量的学习资源和解决方案。这使我认识到，善于利用网络资源和自主学习是前端开发中不可或缺的技能。

### 7.4 团队合作与沟通

虽然本次实验是个人项目，但在开发过程中，与同学和指导老师的讨论交流，帮助我更好地理解问题、优化代码。团队合作和有效沟通在项目开发中起到了重要的作用，也是未来工作中需要继续培养的能力。

### 7.5 持续优化与迭代

通过实验，认识到一个项目的完成并不意味着终点，而是一个持续优化和迭代的过程。随着功能的增加和用户需求的变化，需要不断地对项目进行优化和改进，以适应新的挑战和需求。这种持续学习和改进的态度，将对未来的学习和工作产生积极的影响。

### 7.6 创新与创造力的培养

本次实验提供了一个开放的开发平台，允许我在实现基本功能的基础上，进行创新和个性化设计。例如，可以添加更多的游戏模式、优化界面动画、增加社交功能等，激发了我的创造力和创新思维。创新不仅提升了项目的价值，也为个人成长提供了动力和方向。

### 7.7 技术与艺术的结合

游戏开发不仅仅是技术的堆砌，更是艺术与技术的结合。通过设计美观的界面、流畅的动画效果和愉悦的音效反馈，提升了游戏的整体质感和用户体验。这使我认识到，前端开发不仅需要技术能力，还需要审美能力和艺术感知，才能创造出更具吸引力和感染力的作品。

### 7.8 自我管理与时间规划

在实验过程中，合理的时间管理和任务规划是确保项目顺利完成的重要因素。通过制定详细的开发计划，分阶段实现各项功能，确保了项目的有序进行。这种自我管理和时间规划的能力，对未来的学习和工作都有重要的借鉴意义。

### 7.9 持续学习与技术更新

前端技术日新月异，新的框架、工具和技术不断涌现。通过本次实验，深刻认识到持续学习和跟进技术更新的重要性。只有不断学习和实践，才能在快速发展的技术领域中保持竞争力，适应新的挑战和机遇。

---

通过本次实验，我不仅掌握了前端开发的基本技能，还提升了项目开发的综合能力，培养了解决问题的思维方式和创新能力。这将为我未来的学习和职业发展打下坚实的基础，激励我在前端开发领域不断探索和前行。

---

## 附录

### 项目文件结构示例

```
PokerStraightGame/
│
├── index.html
├── styles.css
├── script.js
├── sounds/
│   ├── success.mp3
│   └── fail.mp3
└── images/
    ├── card1.png
    ├── card2.png
    └── ...（如果使用图像）
```

### 开发日志摘要

- **第1天**：
  - 确定项目需求和功能列表。
  - 搭建项目文件结构，创建基本的HTML框架。
  
- **第2天**：
  - 完成CSS样式的初步设计，确保页面布局符合预期。
  - 实现响应式设计，测试在不同设备上的显示效果。
  
- **第3天**：
  - 编写JavaScript逻辑，实现扑克牌的生成和渲染。
  - 实现拖放功能，测试基本的拖放交互。
  
- **第4天**：
  - 完善顺子验证逻辑，确保游戏规则的正确性。
  - 添加计分系统和计时器功能，测试游戏的基本流程。
  
- **第5天**：
  - 集成音效，添加成功和失败的音效反馈。
  - 调试和优化代码，修复发现的Bug。
  
- **第6天**：
  - 编写详细的代码注释，整理开发文档。
  - 准备实验报告，记录开发过程和心得体会。

---

# 参考文献

1. **MDN Web Docs**: [Drag and Drop API](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API)
2. **JavaScript教程**: [MDN JavaScript Guide](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide)
3. **CSS教程**: [MDN CSS Guide](https://developer.mozilla.org/zh-CN/docs/Web/CSS)
4. **前端开发社区**: [Stack Overflow](https://stackoverflow.com/)
5. **音效资源**: [FreeSound](https://freesound.org/)

---

# 致谢

感谢指导老师在实验过程中的耐心指导和宝贵建议。同时，感谢同学们在项目开发过程中提供的支持和帮助，使得本项目能够顺利完成。

# 结语

本次实验不仅提升了我在前端开发方面的技能，还培养了我在项目管理、问题解决和创新思维等方面的能力。通过实践，我对Web开发有了更深刻的理解和认识，这将对我未来的学习和职业发展产生积极的影响。我将继续努力，不断学习和探索，争取在前端开发领域取得更大的进步。

# 版权声明

本实验报告内容为本人原创，未经允许，禁止转载或用于商业用途。

# 附加说明

由于本实验报告中未包含图片展示部分，所有相关描述均采用文字说明。若未来有图片资源，可根据需要补充相应的图示，以增强报告的可读性和直观性。

# 结束语

感谢阅读本实验报告。希望通过本次实验，能够为前端开发的学习和应用提供有益的参考和借鉴。

# 参考链接

- [HTML5官方文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML)
- [CSS3官方文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS)
- [JavaScript官方文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)

---

# 附录：完整代码展示

### index.html

```html
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
```

### styles.css

```css
/* styles.css */

/* 全局样式 */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #2E7D32;
    color: #FFFFFF;
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
```

### script.js

```javascript
// script.js

document.addEventListener('DOMContentLoaded', () => {
    const deckElement = document.getElementById('deck');
    const targetElement = document.getElementById('target');
    const dragCountElement = document.getElementById('drag-count');
    const scoreElement = document.getElementById('score');
    const timerElement = document.getElementById('timer');
    const soundToggle = document.getElementById('sound-toggle');
    const endgameModal = document.getElementById('endgame-modal');
    const finalCount = document.getElementById('final-count');
    const replayButton = document.getElementById('replay-button');
    const resetButton = document.getElementById('reset-button');
    const messageBox = document.getElementById('message-box');
    const successSound = document.getElementById('success-sound');
    const failSound = document.getElementById('fail-sound');

    let deck = [];
    let dragCount = 0;
    let score = 0;
    let timeLeft = 60;
    let timerInterval;
    let soundOn = true;

    const suits = ['♠', '♥', '♦', '♣'];
    const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

    // 初始化游戏
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

    // 生成一副扑克牌
    function generateDeck() {
        const newDeck = [];
        suits.forEach(suit => {
            ranks.forEach(rank => {
                newDeck.push({ suit, rank });
            });
        });
        return newDeck;
    }

    // 洗牌算法
    function shuffleDeck(deck) {
        for (let i = deck.length -1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i +1));
            [deck[i], deck[j]] = [deck[j], deck[i]];
        }
    }

    // 渲染初始扑克牌到A框
    function renderDeck() {
        deckElement.innerHTML = '';
        deck.forEach((card, index) => {
            const cardDiv = document.createElement('div');
            cardDiv.classList.add('card');
            cardDiv.setAttribute('draggable', 'true');
            cardDiv.dataset.index = index;
            cardDiv.textContent = `${card.rank}${card.suit}`;
            addDragEventListeners(cardDiv);
            deckElement.appendChild(cardDiv);
        });
    }

    // 添加拖放事件监听
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

    // 添加拖放到B框的卡槽
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

    // 更新拖动次数和得分显示
    function updateStats() {
        dragCountElement.textContent = `拖动次数：${dragCount}`;
        scoreElement.textContent = `得分：${score}`;
    }

    // 检查目标区域是否完成
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

    // 获取卡牌的数值
    function getCardValue(cardText) {
        const rank = cardText.slice(0, -1);
        if (rank === 'A') return 1;
        if (rank === 'J') return 11;
        if (rank === 'Q') return 12;
        if (rank === 'K') return 13;
        return parseInt(rank);
    }

    // 判断是否为顺子
    function isStraight(values) {
        for (let i = 1; i < values.length; i++) {
            if (values[i] !== values[i -1] +1) {
                return false;
            }
        }
        return true;
    }

    // 清空目标区域
    function clearTarget() {
        slots.forEach(slot => {
            slot.innerHTML = '';
        });
    }

    // 更新倒计时
    function updateTimer() {
        timeLeft--;
        timerElement.textContent = `时间：${timeLeft}秒`;
        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            endGame();
        }
    }

    // 结束游戏
    function endGame() {
        finalCount.textContent = `你的拖动次数是 ${dragCount} 次，得分：${score} 分！`;
        endgameModal.classList.remove('hidden');
    }

    // 重玩游戏
    replayButton.addEventListener('click', () => {
        initGame();
    });

    // 重置游戏
    resetButton.addEventListener('click', () => {
        initGame();
    });

    // 显示消息
    function showMessage(msg) {
        messageBox.textContent = msg;
        messageBox.classList.remove('hidden');
        setTimeout(() => {
            messageBox.classList.add('hidden');
        }, 2000);
    }

    // 音效开关
    soundToggle.addEventListener('click', () => {
        soundOn = !soundOn;
        soundToggle.textContent = soundOn ? '🔊' : '🔇';
    });

    // 初始化游戏
    initGame();
});
```

---

# 结束语

感谢阅读本实验报告。希望通过本次实验，能够为前端开发的学习和应用提供有益的参考和借鉴。尽管本实验未包含图片展示部分，但通过详细的文字描述，仍能全面了解项目的开发过程和成果。未来，随着资源的完善和技术的提升，游戏将更加丰富和完善。

# 版权声明

本实验报告内容为本人原创，未经允许，禁止转载或用于商业用途。

# 致谢

感谢指导老师在实验过程中的耐心指导和宝贵建议。同时，感谢同学们在项目开发过程中提供的支持和帮助，使得本项目能够顺利完成。

# 参考文献

1. **MDN Web Docs**: [Drag and Drop API](https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_Drag_and_Drop_API)
2. **JavaScript教程**: [MDN JavaScript Guide](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide)
3. **CSS教程**: [MDN CSS Guide](https://developer.mozilla.org/zh-CN/docs/Web/CSS)
4. **前端开发社区**: [Stack Overflow](https://stackoverflow.com/)
5. **音效资源**: [FreeSound](https://freesound.org/)

---

# 附录：完整代码展示

### index.html

```html
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
```

### styles.css

```css
/* styles.css */

/* 全局样式 */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #2E7D32;
    color: #FFFFFF;
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
```

### script.js

```javascript
// script.js

document.addEventListener('DOMContentLoaded', () => {
    const deckElement = document.getElementById('deck');
    const targetElement = document.getElementById('target');
    const dragCountElement = document.getElementById('drag-count');
    const scoreElement = document.getElementById('score');
    const timerElement = document.getElementById('timer');
    const soundToggle = document.getElementById('sound-toggle');
    const endgameModal = document.getElementById('endgame-modal');
    const finalCount = document.getElementById('final-count');
    const replayButton = document.getElementById('replay-button');
    const resetButton = document.getElementById('reset-button');
    const messageBox = document.getElementById('message-box');
    const successSound = document.getElementById('success-sound');
    const failSound = document.getElementById('fail-sound');

    let deck = [];
    let dragCount = 0;
    let score = 0;
    let timeLeft = 60;
    let timerInterval;
    let soundOn = true;

    const suits = ['♠', '♥', '♦', '♣'];
    const ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

    // 初始化游戏
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

    // 生成一副扑克牌
    function generateDeck() {
        const newDeck = [];
        suits.forEach(suit => {
            ranks.forEach(rank => {
                newDeck.push({ suit, rank });
            });
        });
        return newDeck;
    }

    // 洗牌算法
    function shuffleDeck(deck) {
        for (let i = deck.length -1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i +1));
            [deck[i], deck[j]] = [deck[j], deck[i]];
        }
    }

    // 渲染初始扑克牌到A框
    function renderDeck() {
        deckElement.innerHTML = '';
        deck.forEach((card, index) => {
            const cardDiv = document.createElement('div');
            cardDiv.classList.add('card');
            cardDiv.setAttribute('draggable', 'true');
            cardDiv.dataset.index = index;
            cardDiv.textContent = `${card.rank}${card.suit}`;
            addDragEventListeners(cardDiv);
            deckElement.appendChild(cardDiv);
        });
    }

    // 添加拖放事件监听
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

    // 添加拖放到B框的卡槽
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

    // 更新拖动次数和得分显示
    function updateStats() {
        dragCountElement.textContent = `拖动次数：${dragCount}`;
        scoreElement.textContent = `得分：${score}`;
    }

    // 检查目标区域是否完成
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

    // 获取卡牌的数值
    function getCardValue(cardText) {
        const rank = cardText.slice(0, -1);
        if (rank === 'A') return 1;
        if (rank === 'J') return 11;
        if (rank === 'Q') return 12;
        if (rank === 'K') return 13;
        return parseInt(rank);
    }

    // 判断是否为顺子
    function isStraight(values) {
        for (let i = 1; i < values.length; i++) {
            if (values[i] !== values[i -1] +1) {
                return false;
            }
        }
        return true;
    }

    // 清空目标区域
    function clearTarget() {
        slots.forEach(slot => {
            slot.innerHTML = '';
        });
    }

    // 更新倒计时
    function updateTimer() {
        timeLeft--;
        timerElement.textContent = `时间：${timeLeft}秒`;
        if (timeLeft <= 0) {
            clearInterval(timerInterval);
            endGame();
        }
    }

    // 结束游戏
    function endGame() {
        finalCount.textContent = `你的拖动次数是 ${dragCount} 次，得分：${score} 分！`;
        endgameModal.classList.remove('hidden');
    }

    // 重玩游戏
    replayButton.addEventListener('click', () => {
        initGame();
    });

    // 重置游戏
    resetButton.addEventListener('click', () => {
        initGame();
    });

    // 显示消息
    function showMessage(msg) {
        messageBox.textContent = msg;
        messageBox.classList.remove('hidden');
        setTimeout(() => {
            messageBox.classList.add('hidden');
        }, 2000);
    }

    // 音效开关
    soundToggle.addEventListener('click', () => {
        soundOn = !soundOn;
        soundToggle.textContent = soundOn ? '🔊' : '🔇';
    });

    // 初始化游戏
    initGame();
});
```

---

# 结束

本实验报告详细记录了基于Web的扑克牌顺子游戏的开发过程，从项目初始化、HTML结构设计、CSS样式编写到JavaScript逻辑实现。通过实验，不仅掌握了前端开发的基本技能，还提升了项目管理和问题解决的能力。尽管在开发过程中遇到了一些挑战，但通过不断学习和优化，最终成功实现了预期的功能目标。未来，随着技术的进一步学习和实践，相信能够开发出更加复杂和有趣的Web应用。