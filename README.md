# 实践课1：Pi Agent 安装与基础使用实验报告

- 姓名：胡嘉豪
- 学号：202430224010
- GitHub：HNNU-OD
- 实验日期：2026年11月12日
- 实验仓库：[pi-agent-practice](https://github.com/HNNU-OD/pi-agent-practice)

## 一、实验目的

1. 在 Windows 环境下完成 Pi Agent 的安装与启动，理解 Agent 客户端与模型服务之间的关系。
2. 掌握通过 `/login` 配置模型提供商的方法，使用 DeepSeek API 完成基础会话。
3. 掌握 `/model` 和 `/thinking` 命令，理解模型选择与思考级别之间的区别。
4. 使用 `@` 引用本地代码文件，并测试 Agent 对图片内容的理解能力。
5. 从 Pi Packages 安装扩展插件，验证插件加载及实际工具调用功能。
6. 完成 PPT Skill 的项目级安装、启动发现与调用，理解 Extension 与 Skill 的不同作用。

## 二、实验环境

| 项目 | 实际环境 |
| --- | --- |
| 操作系统 | Windows |
| 终端 | Windows PowerShell 5.1 |
| 编辑器 | Visual Studio Code |
| Pi Agent | v1.5.2 |
| 模型提供商 | DeepSeek（API Key 方式） |
| 使用模型 | deepseek-flash、deepseek-v4-pro |
| 实验目录 | `D:\OpenSourceHomework\pi-test` |
| 扩展插件 | pi-web-access |
| Skill | pptx |

## 三、实验任务与结果

### 任务一：本地安装并启动 Pi Agent

在 PowerShell 中执行官方安装命令：

```powershell
powershell -c "irm https://pi.dev/install.ps1 | iex"
```

根据安装器提示选择安装 Pi，并确认将 Pi 的安装目录加入用户 PATH 环境变量。安装完成后关闭原终端，重新打开 PowerShell，在专用实验目录中启动 Pi：

```powershell
cd D:\OpenSourceHomework
mkdir pi-test
cd pi-test
pi
```

启动界面显示 Pi 版本为 v1.5.2，当前工作目录为 `D:\OpenSourceHomework\pi-test`。首次启动时，Pi 自动下载并安装了 fd 文件搜索工具。

界面出现 `No models available` 提示，说明客户端已经正常运行，但尚未配置模型提供商，并不是安装失败。

![S1 Pi安装与启动](images/S1-安装启动.png)

### 任务二：配置模型并完成一次会话

在 DeepSeek 官方 API 平台获取 API Key 后，在 Pi 交互界面输入 `/login`，选择 `Sign in with an API key`，选择 DeepSeek 提供商并输入密钥，完成认证配置。

![模型配置完成](images/配置完成截图.png)

配置成功后，Pi 自动选中了 `deepseek-v4-pro`，底部状态栏显示当前模型及思考级别。考虑到基础实验不需要复杂推理，通过 `/model` 切换到成本较低的 `deepseek-flash`，完成简单对话测试。

模型能够正常返回文本，说明 Pi 客户端与 DeepSeek 模型服务之间的通信链路已建立，API 配置可用。

![S2 基础会话验收](images/任务2验收截图.png)

实验过程中不公开 API Key，也不将保存认证信息的 `auth.json` 提交到 GitHub 仓库。

### 任务三：切换模型与模型思考级别

在 Pi 中输入 `/model` 打开模型选择器，分别选择 `deepseek-flash` 和 `deepseek-v4-pro`，观察底部模型状态栏的变化。切换模型后进行简短问答，验证新模型仍可正常返回结果。

![S3 模型切换后](images/模型切换后.png)

![S3 模型实际回复](images/模型返回结果.png)

随后，通过 `/thinking` 或 `Shift+Tab` 调整当前模型支持的思考级别。实验中观察到了 `thinking off`、`low` 和 `high` 等状态，验证了推理设置可以在会话中调整。

![S3 思考级别切换](images/思考级别切换图.png)

通过本任务，理解了模型切换是改变实际调用的模型，而思考级别切换是调整支持该功能的模型的推理设置。对于简单对话和代码解释，可以使用 Flash 及较低思考级别；对于复杂分析和多步骤任务，可以尝试 Pro 或较高思考级别，但应考虑响应时间与 API 用量。

### 任务四：@ 引用代码文件与图片识别

#### A. 使用 @ 引用代码文件

在实验目录中创建 `hello.py`，编写一个简单的加法函数：

```python
def add(a, b):
    return a + b

print(add(2, 3))
```

在 Pi 输入区使用 `@` 选择本地代码文件，并要求模型解释该文件的功能及运行结果，不修改源代码。

模型解释了 `add(a, b)` 函数用于返回两个数之和，并指出 `print(add(2, 3))` 的输出结果为 `5`，与程序逻辑一致。

![S4 代码文件引用与解释](images/@代码.png)

本任务验证了 Agent 可以获取本地文件上下文，并根据代码内容进行分析和解释。

#### B. 图片输入与识别

准备一张包含蓝色正方形和黑色数字 7 的小图片，保存为 `testimage.png`。

原始测试图片：

![原始测试图片](testimage.png)

在 Pi 中提供该图片的本地路径，并发送提示词“请描述图片中的颜色、形状和数字”。Pi 调用 `read` 工具读取图片，并返回识别结果：白色背景、蓝色实心正方形及黑色数字 7。识别结果与原始图片一致。

![S5 图片读取与识别](images/图片返回截图.png)

本次截图证明了通过文件读取工具进行图片识别的能力。指导书另要求使用剪贴板粘贴图片（Windows 中可尝试 Alt+V），该方式需要单独截图验证，不能用文件路径读取代替粘贴证据。

<!-- 补充实际剪贴板粘贴测试后，在此插入对应截图及结果。 -->

### 任务五：安装并使用一个插件

从 [Pi Packages](https://pi.dev/packages) 选择 `pi-web-access` 扩展插件，用于为 Agent 提供网页访问与内容抓取能力。

退出 Pi 后，在 PowerShell 中执行个人级安装命令：

```powershell
pi install npm:pi-web-access
```

![S6 插件安装完成](images/插件安装完成.png)

安装后完全退出原会话，在实验目录重新运行 `pi`，检查扩展加载信息，确认 `pi-web-access` 已被发现并加载。

![S6 插件加载](images/插件安装完成显示图.png)

为了验证插件的实际功能，向 Pi 发送提示词，要求访问 `https://example.com`，获取网页标题及主要内容。

![S6 插件访问网页](images/使用插件介绍访问网页.png)

实验结果显示网页标题为 `Example Domain`，并正确说明该域名主要用于文档和示例演示。模型回复记录了 `fetch_content` 的使用情况：首次采用 readable 模式时，页面因内容过于简短而未能正常解析，随后改用 raw 模式获取 HTML 原文，成功取得标题和正文。

![S6 插件返回结果](images/使用插件返回结果.png)

本任务验证了扩展插件能够为 Agent 提供额外的网页访问工具，使模型不仅能够进行文本问答，还可以通过工具获取外部信息。

### 任务六：让 AI 安装 PPT Skill（选做）

在实验项目中启动 Pi，要求 Agent 从 [anthropics/skills](https://github.com/anthropics/skills) 下载 `skills/pptx` 完整目录，并安装到当前项目的 `.pi/skills/pptx` 下。要求保留 `SKILL.md`、许可证及 `scripts/` 等支持文件，不自动安装额外依赖，并检查安装目录。

在 VS Code 中检查项目目录，确认 PPT Skill 安装位置为：

```text
D:\OpenSourceHomework\pi-test\.pi\skills\pptx
```

主要目录结构如下（具体文件以实际下载版本为准）：

```text
pi-test/
└── .pi/
    └── skills/
        └── pptx/
            ├── SKILL.md
            ├── LICENSE.txt
            └── scripts/
```

![S7 PPT Skill项目目录](images/skills加入后项目树截图.png)

安装完成后退出原会话，并在项目目录中重新运行 `pi`。通过启动资源列表检查 `pptx` Skill 的发现状态，再输入显式调用命令：

```text
/skill:pptx 请说明这个 Skill 的名称、用途和所在目录。
```

通过模型回复了解该 Skill 的用途：提供 PowerPoint 文件创建、编辑、读取及检查等任务的操作说明与支持脚本。

![S8 PPT Skill调用结果](images/介绍skillptx.png)

本任务重点是验证 Skill 的项目级安装、发现与调用。实际生成 PPT 文件属于进一步的选做内容，本报告不将其作为已完成的实验结果。

<!-- 如已保存重启后 [Skills] 列表截图，请在此补充；源 commit 也应根据本人实际下载记录填写。 -->

## 四、实验总结

通过本次实验，我对 Pi Agent 的运行方式以及 AI 编程助手的扩展机制有了进一步认识。

1. **客户端与模型服务解耦。** Pi 本身不是大语言模型，而是用于组织会话、工具和任务上下文的客户端。配置 DeepSeek API 后才能使用对应模型服务，首次启动出现 `No models available` 不代表安装失败。

2. **模型选择与思考级别需要结合任务需求。** Flash 模型可以满足简单问答与基础代码解释，Pro 模型及较高推理级别适合更复杂的任务，但可能增加响应时间和 API 用量。

3. **文件上下文与多模态输入扩展了 Agent 的能力。** 使用 `@` 引用代码文件，使模型可以结合真实源代码给出解释；通过图片读取测试，验证了模型对颜色、形状和数字的识别能力。

4. **Extension 与 Skill 的作用不同。** Extension 为 Agent 增加工具、命令或其他运行时功能，例如 `pi-web-access` 提供网页访问能力；Skill 则通过 `SKILL.md` 和支持文件提供特定任务的操作指导。安装、发现及调用需要分别验证。

5. **实际运行环境与错误处理同样重要。** 网页访问实验中，readable 模式未能正确解析页面，改用 raw 模式后成功获取内容。实验还观察到 Pi 的 Bash 工具尝试调用 WSL，而当前 Windows 环境未配置 WSL；对于本次网页任务，可使用原生网页访问工具绕开该问题。后续执行本地 Shell 操作时，应根据实际环境配置适用的 Shell。

本次实践让我认识到，AI Agent 的价值不仅在于模型本身的问答能力，还在于将模型与本地文件、外部工具及任务技能结合，从而完成更完整的软件开发辅助流程。
