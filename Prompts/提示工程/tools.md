# 提示工程工具

_最后更新于 **2024年8月7日**，作者 **Sander Schulhoff**_

本节包含一系列对于提示工程有用的非IDE工具的列表。

## 提示开发、测试与链接

### [LangChain](https://github.com/hwchase17/langchain/)

> 大型语言模型（LLMs）正成为一种变革性技术，使开发者能够构建之前无法实现的应用程序。但仅使用这些LLMs通常不足以创造出真正强大的应用程序 - 真正的力量来自于您能将它们与其他计算或知识源结合使用。
>
> 该库旨在协助开发此类应用程序。

### [PromptAppGPT](https://github.com/mleoking/PromptAppGPT)

> PromptAppGPT 是一个基于提示的低代码快速应用开发框架。PromptAppGPT包含低代码提示基础开发、GPT文本生成、DALLE图像生成、在线提示编辑器+编译器+运行器、自动用户界面生成、支持插件扩展等功能。PromptAppGPT旨在实现基于GPT的自然语言应用开发。
>
> PromptAppGPT 提供多任务条件触发、结果验证和失败重试功能，允许将本需要多步骤手动生成的任务自动化。同时，用户不再需要记忆并输入繁琐的提示口诀，只需输入任务所需的核心必要信息即可轻松完成任务。
>
> PromptAppGPT显著降低了GPT应用开发的门槛，使任何人都可以用几行低代码开发AutoGPT类应用。

### [Prompt-generator-for-ChatGPT](https://github.com/rubend18/Prompt-generator-for-ChatGPT)

> “ChatGPT提示生成器”应用程序是一个桌面工具，旨在帮助用户为OpenAI开发的聊天机器人模型ChatGPT生成特定角色的提示。

### [Dust.tt](https://dust.tt/)

> Dust平台通过一系列对外部模型的提示调用来帮助构建大型语言模型应用。它提供了一个易于使用的图形界面来构建提示链，以及一套标准块和一个自定义编程语言来解析和处理语言模型输出。
>
> 它提供了一系列功能，使应用程序的开发更快、更容易且更稳定：

- 并行运行多个完成任务
- 检查执行输出
- 版本化提示链
- 自定义编程语言处理数据和文本
- API集成多种模型和外部服务

### [OpenPrompt](https://thunlp.github.io/OpenPrompt/)[1](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> 提示学习是适应预训练语言模型（PLMs）到下游NLP任务的最新范式，它通过文本模板修改输入文本，并直接使用PLMs进行预训练任务。OpenPrompt是基于PyTorch构建的库，提供了一个标准、灵活和可扩展的框架来部署提示学习流水线。OpenPrompt支持直接从huggingface transformers加载PLMs。未来，我们也将支持其他库实现的PLMs。

### [BetterPrompt](https://github.com/stjordanis/betterprompt)

> ⚡ 在推送到生产环境前为LLM提示进行测试的测试套件 ⚡

### [Prompt Engine](https://github.com/microsoft/prompt-engine)

> 用于创建和维护大型语言模型（LLMs）提示的NPM实用程序库。

### [Promptify](https://github.com/promptslab/Promptify)

> 仅依靠LLMs通常不足以构建应用程序和工具。为了释放它们的全部潜力，有必要将LLMs与其他计算或知识源整合，并为生产环境准备好管道。
>
> 这个库旨在协助开发一个用于生产中使用LLMs API的管道，解决NLP任务如命名实体识别、分类、问题回答、摘要、Text2Graph等，并为不同任务构建聊天代理提供强大的代理。

### [PromptFlow](https://github.com/microsoft/promptflow)

> PromptFlow是一个免费的开源低代码工具，允许用户整合LLMs、提示、Python函数和条件逻辑来创建流程图。它包括以下节点：
>
> OpenAI API调用（任何模型，包括Whisper语音转文本）
>
> Anthropic Claude Calls, 任意Python代码块, 长短期历史管理
>
> 数据库查询, PostgresML集成, 文本嵌入
>
> HTTP请求, SerpAPI Google搜索, 和ElevenLabs语音合成文档可在[这里](https://www.promptflow.org/en/latest/index.html)找到

### [TextBox](https://github.com/RUCAIBox/TextBox)[2](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> TextBox 2.0 是一个基于Python和PyTorch的最新文本生成库，专注于构建统一和标准化的流水线，将预训练语言模型应用于文本生成：

### [ThoughtSource](https://github.com/OpenBioLink/ThoughtSource)

> "ThoughtSource是一个集中的、开放的资源和社区，专注于大型语言模型（LLMs）的链式思维推理的数据和工具（Wei 2022）。我们的长期目标是在先进的AI系统中实现可靠和稳健的推理，以推动科学研究和医学实践。"

## 其他

### [GPT Index](https://gpt-index.readthedocs.io/en/latest/)[3](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> GPT Index是一个项目，包含了一套数据结构，旨在使大型外部知识库更容易与LLMs一起使用

### [Deforum](https://github.com/HelixNGC7293/DeforumStableDiffusionLocal)

> AI动画视频

### [Visual Prompt Builder](https://tools.saxifrage.xyz/prompt)

> 可视化构建提示

### [Interactive Composition Explorer](https://github.com/oughtinc/ice)

> ICE是一个Python库和语言模型程序的跟踪可视化器。

### [PTPT - Prompt To Plain Text](https://github.com/LeslieLeung/PTPT)

> PTPT是一个命令行工具，允许您在ChatGPT的帮助下轻松转换纯文本文件使用预定义的提示。使用PTPT，您可以轻松创建和分享提示格式，使协作和自定义变得轻而易举。此外，通过订阅，您可以访问更多提示，增强您的体验。如果您对提示工程感兴趣，可以使用PTPT开发和分享您的提示。

### [Orquesta AI Prompts](https://orquesta.cloud/platform/ai-llm-prompts)

> AI提示的低代码协作平台

- 完整的提示生命周期管理（从创意到反馈收集）
- 企业级功能和安全性
- 支持公共、私有和自定义LLMs
- 基于自定义背景和业务规则的提示。边缘上的评估
- 实时记录和性能及提示经济的收集

---
# Prompt Engineering Tools

_Last updated on **August 7, 2024** by **Sander Schulhoff**_

This section contains a list of non-IDE tools that are useful for prompting.

## Prompt Development, Testing, and Chaining

### [LangChain](https://github.com/hwchase17/langchain/)

> Large language models (LLMs) are emerging as a transformative technology, enabling developers to build applications that they previously could not. But using these LLMs in isolation is often not enough to create a truly powerful app - the real power comes when you can combine them with other sources of computation or knowledge.
> 
> This library is aimed at assisting in the development of those types of applications.

### [PromptAppGPT](https://github.com/mleoking/PromptAppGPT)

> PromptAppGPT is a low-code prompt-based rapid app development framework. PromptAppGPT contains features such as low-code prompt-based development, GPT text generation, DALLE image generation, online prompt editer+compiler+runer, automatic user interface generation, support for plug-in extensions, etc. PromptAppGPT aims to enable natural language app development based on GPT.
> 
> PromptAppGPT provides multi-task conditional triggering, result verification, and failure retry capabilities, allowing manual generation tasks that would otherwise require multiple steps to be automated. At the same time, users no longer need to memorise and enter the tedious prompt mantra themselves, and can easily complete tasks by entering only the core necessary information for the task.
> 
> PromptAppGPT significantly lowers the barrier to GPT application development, allowing anyone to develop AutoGPT-like applications with a few lines of low code.

### [Prompt-generator-for-ChatGPT](https://github.com/rubend18/Prompt-generator-for-ChatGPT)

> The "Prompt generator for ChatGPT" application is a desktop tool designed to help users generate character-specific prompts for ChatGPT, a chatbot model developed by OpenAI.

### [Dust.tt](https://dust.tt/)

> The Dust platform helps build large language model applications as a series of prompted calls to external models. It provides an easy to use graphical UI to build chains of prompts, as well as a set of standard blocks and a custom programming language to parse and process language model outputs.
> 
> It provides a series of features to make development of applications faster, easier and more robust:

- running multiple completions in parallel
- inspecting execution outputs
- versioning prompt chains
- custom programming language to process data and text
- API integration for various models and external services

### [OpenPrompt](https://thunlp.github.io/OpenPrompt/)[1](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> Prompt-learning is the latest paradigm to adapt pre-trained language models (PLMs) to downstream NLP tasks, which modifies the input text with a textual template and directly uses PLMs to conduct pre-trained tasks. OpenPrompt is a library built upon PyTorch and provides a standard, flexible and extensible framework to deploy the prompt-learning pipeline. OpenPrompt supports loading PLMs directly from huggingface transformers. In the future, we will also support PLMs implemented by other libraries.

### [BetterPrompt](https://github.com/stjordanis/betterprompt)

> ⚡ Test suite for LLM prompts before pushing them to PROD ⚡

### [Prompt Engine](https://github.com/microsoft/prompt-engine)

> NPM utility library for creating and maintaining prompts for Large Language Models (LLMs).

### [Promptify](https://github.com/promptslab/Promptify)

> Relying solely on LLMs is often insufficient to build applications & tools. To unlock their full potential, it's necessary to integrate LLMs with other sources of computation or knowledge and get the pipeline ready for production.
> 
> This library is aimed at assisting in developing a pipeline for using LLMs APIs in production, solving NLP Tasks such as NER, Classification, Question, Answering, Summarization, Text2Graph etc. and providing powerful agents for building chat agents for different tasks.

### [PromptFlow](https://github.com/microsoft/promptflow)

> PromptFlow is a free, open-source, low-code tool that allows users to integrate LLMs, prompts, Python functions, and conditional logic to create flowcharts. It includes nodes for:
> 
> OpenAI API Calls (any model, including Whisper speech-to-text)
> 
> Anthropic Claude Calls, Arbitrary Python Code blocks, and Long + Short term history management
> 
> Database Queries, PostgresML integration, and Text Embeddings
> 
> HTTP Requests, SerpAPI Google Searches, and ElevenLabs Speech Synthesis Documentation can be found [here](https://www.promptflow.org/en/latest/index.html)

### [TextBox](https://github.com/RUCAIBox/TextBox)[2](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> TextBox 2.0 is an up-to-date text generation library based on Python and PyTorch focusing on building a unified and standardized pipeline for applying pre-trained language models to text generation:

### [ThoughtSource](https://github.com/OpenBioLink/ThoughtSource)

> "ThoughtSource is a central, open resource and community centered on data and tools for chain-of-thought reasoning in large language models (Wei 2022). Our long-term goal is to enable trustworthy and robust reasoning in advanced AI systems for driving scientific research and medical practice."

## Misc.

### [GPT Index](https://gpt-index.readthedocs.io/en/latest/)[3](https://learnprompting.org/zh-Hans/docs/tooling/tools#footnotes)

> GPT Index is a project consisting of a set of data structures designed to make it easier to use large external knowledge bases with LLMs

### [Deforum](https://github.com/HelixNGC7293/DeforumStableDiffusionLocal)

> AI animated videos

### [Visual Prompt Builder](https://tools.saxifrage.xyz/prompt)

> Build prompts, visually

### [Interactive Composition Explorer](https://github.com/oughtinc/ice)

> ICE is a Python library and trace visualizer for language model programs.

### [PTPT - Prompt To Plain Text](https://github.com/LeslieLeung/PTPT)

> PTPT is an command-line tool that allows you to easily convert plain text files using pre-defined prompts with the help of ChatGPT. With PTPT, you can effortlessly create and share prompt formats, making collaboration and customization a breeze. Plus, by subscribing, you gain access to even more prompts to enhance your experience. If you're interested in prompt engineering, you can use PTPT to develop and share your prompts.

### [Orquesta AI Prompts](https://orquesta.cloud/platform/ai-llm-prompts)

> Low-code collaboration platform for AI Prompts

- Full prompt lifecycle management (from ideation to feedback collection)
- Enterprise-grade features and security
- Support for public, private, and custom LLMs
- Prompts based on custom context and business rules. Evaluations on the Edge
- Real-time logging and collection of performance and prompt economics

### Other

[https://gpttools.com](https://gpttools.com/)

## Footnotes

1. Ding, N., Hu, S., Zhao, W., Chen, Y., Liu, Z., Zheng, H.-T., & Sun, M. (2021). OpenPrompt: An Open-source Framework for Prompt-learning. arXiv Preprint arXiv:2111.01998. [↩](https://learnprompting.org/zh-Hans/docs/tooling/tools#user-content-fnref-1)
    
2. Tang, T., Junyi, L., Chen, Z., Hu, Y., Yu, Z., Dai, W., Dong, Z., Cheng, X., Wang, Y., Zhao, W., Nie, J., & Wen, J.-R. (2022). TextBox 2.0: A Text Generation Library with Pre-trained Language Models. [↩](https://learnprompting.org/zh-Hans/docs/tooling/tools#user-content-fnref-2)
    
3. Liu, J. (2022). GPT Index. [https://doi.org/10.5281/zenodo.1234](https://doi.org/10.5281/zenodo.1234) [↩](https://learnprompting.org/zh-Hans/docs/tooling/tools#user-content-fnref-3)