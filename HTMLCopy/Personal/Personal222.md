```html
<!DOCTYPE html>

<html lang="zh">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>个人主页</title>

    <link rel="stylesheet" href="styles.css">

</head>

<body>

    <header>

        <h1>个人主页</h1>

        <nav>

            <a href="#about">关于我</a>

            <a href="#education">教育背景</a>

            <a href="#interests">兴趣爱好</a>

            <a href="#contact">联系方式</a>

        </nav>

    </header>

  

    <main>

        <section id="about">

            <h2>关于我</h2>

            <img src="/1.jpg" alt="个人照片" class="profile-photo">

            <p><strong>姓名:</strong> 安春秋</p>

            <p><strong>学号:</strong> 4221114024</p>

            <p>欢迎来到我的个人主页！这是一个展示我个人信息的地方。</p>

        </section>

  

        <section id="education">

            <h2>教育背景</h2>

            <p>我目前在江苏大学京江学院学习软件工程专业。</p>

        </section>

  

        <section id="interests">

            <h2>兴趣爱好</h2>

            <ul>

                <li>编程</li>

                <li>阅读</li>

                <li>旅行</li>

            </ul>

        </section>

  

        <section id="contact">

            <h2>联系方式</h2>

            <p><strong>Email:</strong> anchunqiu@example.com</p>

            <p><strong>电话:</strong> 123-456-7890</p>

        </section>

    </main>

  

    <footer>

        <p>&copy; 2024 安春秋. 版权所有.</p>

    </footer>

</body>

</html>
```

```css
body {

    font-family: Arial, sans-serif;

    line-height: 1.6;

    margin: 0;

    padding: 0;

    background-color: #f4f4f4;

}

header {

    background: #282c34;

    color: #61dafb;

    padding: 20px 0;

    text-align: center;

    box-shadow: 0 4px 6px rgba(0,0,0,0.1);

}

nav {

    margin: 0;

    padding: 0;

    text-align: center;

    background: #61dafb;

}

nav a {

    color: #282c34;

    text-decoration: none;

    padding: 15px 20px;

    display: inline-block;

    font-weight: bold;

}

nav a:hover {

    background: #282c34;

    color: #61dafb;

    border-radius: 4px;

}

main {

    padding: 20px;

    max-width: 1200px;

    margin: auto;

}

.profile-photo {

    max-width: 150px;

    border-radius: 50%;

    border: 4px solid #61dafb;

    display: block;

    margin: 0 auto;

}

section {

    margin-bottom: 30px;

    background: #fff;

    padding: 20px;

    border-radius: 8px;

    box-shadow: 0 2px 4px rgba(0,0,0,0.1);

}

h2 {

    border-bottom: 2px solid #61dafb;

    padding-bottom: 10px;

    margin-bottom: 20px;

    color: #282c34;

}

footer {

    background: #282c34;

    color: #61dafb;

    text-align: center;

    padding: 15px 0;

    position: fixed;

    bottom: 0;

    width: 100%;

}

@media (max-width: 768px) {

    nav a {

        display: block;

        padding: 10px;

        font-size: 14px;

    }

    .profile-photo {

        max-width: 120px;

    }

}
```