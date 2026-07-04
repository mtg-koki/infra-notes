# Git / GitHub 運用手順（このリポジトリ向け）

## 概要

このInfraリポジトリ（`https://github.com/mtg-koki/infra-notes.git`）の変更をGitHubへ反映するための手順。新しい端末でのセットアップから、日常の編集→コミット→プッシュまでをまとめる。クラウド固有の内容ではないが、リポジトリ運用上の共通手順としてここに置く。

## 初回セットアップ（新しい端末の場合）

1. Gitがインストールされているか確認: `git --version`
2. ユーザー情報を設定（未設定の場合）
   - `git config --global user.name "名前"`
   - `git config --global user.email "メールアドレス"`
3. リポジトリを取得
   - 既にローカルにクローン済みならスキップ
   - 新規に取得する場合: `git clone https://github.com/mtg-koki/infra-notes.git`
4. GitHubへのpush認証を準備（いずれか）
   - HTTPS + Personal Access Token（初回push時にユーザー名とトークンの入力を求められ、資格情報マネージャーに保存される）
   - GitHub CLI（`gh auth login`）でHTTPS/SSHの認証を設定
   - SSH鍵を作成し、GitHubアカウントに登録した上でリモートURLをSSH形式に変更
5. リモート設定を確認: `git remote -v` で`origin`が正しいURL・fetch/push両方向になっているか確認

## 日常の編集→プッシュ手順

1. 現在の状態を確認: `git status`
2. 変更差分を確認: `git diff`
3. 変更したファイルを個別に指定してステージ
   - `git add <ファイルパス>`（`git add -A` や `git add .` は意図しないファイルを巻き込む可能性があるため避ける）
4. コミットを作成: `git commit -m "変更内容が分かる一言"`
5. リモートに他の変更がある場合は取り込む: `git pull --rebase origin main`
6. プッシュ: `git push origin main`
7. 反映確認: `git log --oneline -3` やGitHub上のリポジトリページでコミットが反映されているか確認

## 注意点・ベストプラクティス

- pushする前に`git status`で意図しないファイル（認証情報、一時ファイル、大きなバイナリなど）が含まれていないか確認する。
- コミットメッセージは「何を変えたか」より「なぜ変えたか」が伝わるように書く。
- `git push --force`やコミット履歴を書き換える操作（`git rebase -i`、`git reset --hard`後のpushなど）は、共有ブランチ（`main`など）では基本的に使わない。
- 複数人・複数端末で同じブランチを触る場合は、pushの前に`git pull`で最新化し、コンフリクトが出たらその場で解消してからpushする。
- `.gitignore`で除外すべきファイル（認証情報、ローカル設定など）は早い段階で整備しておく。

## ベストプラクティス

- （実務で得た知見をここに追記する）
