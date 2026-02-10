# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** 是一个托管在 GitHub 上的自动化工具，会根据你订阅的主题每天发送包含最新 arXiv 论文的 HTML 邮件。

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## 工作原理

- 读取 `FREELOADERS` 环境变量，获取收件人邮箱和订阅标签。
- 针对每个主标签从 `http://rss.arxiv.org/rss/<primary-tag>` 拉取 RSS。
- 仅保留标签与次级标签列表有交集的论文。
- 使用 `subject_list.csv` 将标签代码映射为邮件中的完整主题名称。
- 通过 `template/daily_feed.html` 渲染 HTML，并使用 Gmail SMTP 发送邮件。

## 前置条件

- 一个 GitHub 账号，用于 fork 仓库并运行 GitHub Actions。
- 一个 Gmail 账号和应用专用密码（参见 [Gmail 应用专用密码](https://support.google.com/mail/answer/185833?hl=en-GB)）。
- `subject_list.csv` 中列出的 arXiv 主题标签（需要时自行补充）。
- 需要本地运行时的 Python 3.x 环境。

## 安装与配置（GitHub Actions）

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
   | Variable | `FREELOADERS` | 收件人邮箱与订阅标签。 |

4. **配置 `FREELOADERS` 变量**  
   每一行代表一个收件人，主题标签组之间用冒号分隔：

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - 每组标签的第一个是主标签。
   - 每个主标签都需要至少一个次级标签。若希望收到该主标签下的所有论文，请在次级标签中包含主标签本身（如 `math.NT,math.NT`）。

5. **按需更新 `subject_list.csv`**  
   如果缺少主题标签，请添加对应的标签和全称。

6. **调整定时计划（可选）**  
   工作流默认在 **UTC 时间 10:00，周一至周五** 运行。如需更改，请修改
   `.github/workflows/math-email.yml` 中的 cron 表达式。

## 本地运行

1. 安装依赖：

   ```bash
   python -m pip install -r requirements.txt
   ```

2. 创建 `.env` 文件（由 `python-dotenv` 加载）并填写凭据和订阅配置：

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. 运行脚本：

   ```bash
   python daily_runner.py
   ```

## 常见问题

- **没有收到邮件**：查看 GitHub Actions 日志并确认工作流完成。
- **Gmail 登录失败**：确认使用应用专用密码且已启用两步验证。
- **主题标签出现 `ValueError`**：检查该标签是否存在于 `subject_list.csv` 中。
- **邮件内容为空**：确保次级标签与论文标签有交集（想获取全部论文时请在次级标签中包含主标签）。

## 贡献

欢迎贡献！如有问题或建议，请先创建 issue，再从你的 fork 提交 pull request。

## 许可证

本项目采用 GPL-3.0 许可证。
