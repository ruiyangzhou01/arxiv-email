# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** は、購読している arXiv の分野に合わせて最新論文を毎日 HTML メールで送る GitHub ホストの自動化です。

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## 仕組み

- `FREELOADERS` 環境変数を読み取り、宛先と購読タグを取得します。
- 各主タグごとに `http://rss.arxiv.org/rss/<primary-tag>` から RSS を取得します。
- 論文のタグが主タグのセカンダリタグ一覧と交差するものだけを残します。
- `subject_list.csv` でタグコードを正式名称に変換してメールに表示します。
- `template/daily_feed.html` で HTML をレンダリングし、Gmail SMTP で送信します。

## 必要条件

- GitHub Actions を動かすための GitHub アカウント。
- Gmail アカウントとアプリパスワード（[Gmail のアプリ パスワード](https://support.google.com/mail/answer/185833?hl=en-GB)）。
- `subject_list.csv` に掲載されている arXiv タグ（必要なら追記）。
- ローカル実行時の Python 3.x。

## セットアップ（GitHub Actions）

1. **リポジトリを fork**  
   GitHub で自分の fork を作成します。

2. **Gmail のアプリパスワードを作成**  
   Gmail SMTP を使うため、Gmail アカウントのアプリパスワードが必要です。

3. **GitHub Secrets と Variables を追加**  
   fork したリポジトリで **Settings** → **Secrets and variables** → **Actions** を開き、次を追加します。

   | 種類 | 名前 | 説明 |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | 送信に使う Gmail アドレス。 |
   | Secret | `GMAIL_PASSWORD` | Gmail のアプリパスワード。 |
   | Variable | `FREELOADERS` | 宛先と購読タグ。 |

4. **`FREELOADERS` を設定**  
   1 行が 1 宛先で、タグのグループをコロンで区切ります。

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - 各グループの最初が主タグです。
   - 主タグには少なくとも 1 つのセカンダリタグが必要です。主タグの全件を受け取りたい場合は、主タグ自身をセカンダリに含めてください（例: `math.NT,math.NT`）。

5. **`subject_list.csv` を必要に応じて更新**  
   不足しているタグと名称を追加します。

6. **スケジュールを変更（任意）**  
   ワークフローは既定で **UTC 10:00、月〜金** に実行されます。必要に応じて
   `.github/workflows/math-email.yml` の cron を変更してください。

## ローカルで実行

1. 依存関係をインストールします。

   ```bash
   python -m pip install -r requirements.txt
   ```

2. `.env` ファイル（`python-dotenv` が読み込みます）に認証情報と購読を記入します。

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. スクリプトを実行します。

   ```bash
   python daily_runner.py
   ```

## トラブルシューティング

- **メールが届かない**: GitHub Actions のログを確認し、ワークフローが完了しているかチェックします。
- **Gmail ログインに失敗する**: アプリパスワードを使用し、2FA を有効にしてください。
- **タグで `ValueError` が出る**: `subject_list.csv` にタグがあるか確認します。
- **メールが空になる**: セカンダリタグが arXiv のタグと交差するようにしてください（全件取得なら主タグをセカンダリに含めます）。

## 貢献

バグ報告や改善提案は issue で共有してください。準備ができたら fork から pull request を送ってください。

## ライセンス

このプロジェクトは GPL-3.0 ライセンスです。
