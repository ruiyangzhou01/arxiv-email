# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md)

**arxiv-email** 是一个托管在 GitHub 上的自动化工具，会根据你订阅的主题每天发送包含最新 arXiv 论文的 HTML 邮件。

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## 概览

- 从 arXiv 主题分类拉取 RSS。
- 根据你配置的主分类与可选次级标签过滤论文。
- 使用 `template/daily_feed.html` 渲染格式化的 HTML 邮件。
- 通过 GitHub Actions 定时运行，并使用 Gmail 发送邮件。

## 前置条件

- 一个 GitHub 账号，用于 fork 仓库并运行 GitHub Actions。
- 一个 Gmail 账号和应用专用密码（参见 [Gmail 应用专用密码](https://support.google.com/mail/answer/185833?hl=en-GB)）。
- 了解常见的 arXiv 主题标签（例如 `math.AG`、`cs.LG`）。

## 安装与配置

1. **Fork 本仓库**  
   在 GitHub 上创建你自己的 fork。

2. **创建 Gmail 应用专用密码**  
   本仓库使用 Gmail SMTP，因此需要为你的 Gmail 账号生成应用专用密码。

3. **添加 GitHub Secrets 和 Variables**  
   在 fork 的仓库中依次进入 **Settings** → **Secrets and variables** → **Actions**，然后添加：

   | 类型 | 名称 | 说明 |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | 用于发送邮件的 Gmail 地址。 |
   | Secret | `GMAIL_PASSWORD` | 对应账号的 Gmail 应用专用密码。 |
   | Variable | `FREELOADERS` | 收件人邮箱与订阅主题配置。 |

4. **配置 `FREELOADERS` 变量**  
   每一行代表一个收件人，使用冒号分隔主题标签：

   ```text
   example0@gmail.com:primary1,secondary11,secondary12:primary2:primary3,secondary31
   example1@gmail.com:quant-ph,cs.LG,cs.CV,cs.AI,cs.GR:math.AG
   example2@gmail.com:math.NT
   ```

   上述示例会转换为：

   ```python
   SUBSCRIPTIONS0 = {
       "primary1": ["secondary11", "secondary12"],
       "primary2": [],
       "primary3": ["secondary31"],
   }

   SUBSCRIPTIONS1 = {
       "quant-ph": ["cs.LG", "cs.CV", "cs.AI", "cs.GR"],
       "math.AG": [],
   }

   SUBSCRIPTIONS2 = {
       "math.NT": [],
   }
   ```

5. **按需更新 `subject_list.csv`**  
   如果缺少主题标签，请添加对应的标签和全称。

6. **调整定时计划（可选）**  
   工作流默认在 **UTC 时间 10:00，周一至周五** 运行。如需更改，请修改
   `.github/workflows/math-email.yml` 中的 cron 表达式。

## 使用方法

### GitHub Actions（推荐）

`math-email` 工作流会按上述计划运行，也可以在 GitHub 的 Actions 页面手动触发。

### 本地运行

1. 安装依赖：

   ```bash
   python -m pip install -r requirements.txt
   ```

2. 创建 `.env` 文件并填写凭据和订阅配置：

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT
   colleague@example.com:cs.LG,cs.AI"
   ```

3. 运行脚本：

   ```bash
   python daily_runner.py
   ```

## 常见问题

- **没有收到邮件**：查看 GitHub Actions 日志并确认工作流完成。
- **Gmail 登录失败**：确认使用应用专用密码且已启用两步验证。
- **主题标签出现 `ValueError`**：检查该标签是否存在于 `subject_list.csv` 中。

## 贡献

欢迎贡献！如有问题或建议，请先创建 issue，再从你的 fork 提交 pull request。

## 许可证

本项目采用 GPL-3.0 许可证。
