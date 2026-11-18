# BookShelf — Playwright E2E テスト練習環境

本番試験（TaskFlow）と同じ構造の練習用環境です。
BookShelf（書籍管理サイト）を題材に、POM パターンと AAA パターンを練習できます。

## テスト対象サイト

**https://ssi-bookshelf-practice.web.app**

公開済みなので、ローカルでサーバーを立てる必要はありません。

**テストの中ではログインが必要です。** 各 practice ファイルの `beforeEach` で
`loginPage.loginAsAdmin()` を呼んでから、対象ページへ遷移してください。
`playwright.config.ts` の `baseURL` がこのURLを既定にしているので、そのまま `npx playwright test` で動きます。

まずブラウザでこのサイトを開いて触ってみてください。
どんな画面があるのかを掴んでからテストを書き始めた方が、意味のあるテストになります。

## 最初にやること（Fork して手元に持ってくる）

このリポジトリには書き込み権限を配っていません。
そのため、**自分のアカウントの下にコピーを作ってから**作業します。この操作を Fork と言います。

同じ中身が3か所にあると思ってください。

```
［本家］      ryujikawakami-jpg/Simulation_PracticalSkillsSite_POM   ← 問題と模範解答を直すところ
   │ Fork（押した瞬間のコピーが作られる）
［自分のfork］<自分のアカウント>/Simulation_PracticalSkillsSite_POM   ← Pull Request を出す起点
   │ clone
［手元］      自分のPC
```

**Fork は押した瞬間のコピーです。あとから本家が変わっても、自動では追いつきません。**
だから本家の置き場所にも名前を付けておきます。慣習で `upstream` と呼びます。

```bash
# 1. GitHubでこのリポジトリを開き、右上の Fork を押す

# 2. 自分のforkを手元に持ってくる（URLは自分のアカウント名）
git clone https://github.com/<自分のアカウント>/Simulation_PracticalSkillsSite_POM.git
cd Simulation_PracticalSkillsSite_POM

# 3. 本家に upstream という名前を付ける（一度やれば残ります）
git remote add upstream https://github.com/ryujikawakami-jpg/Simulation_PracticalSkillsSite_POM.git

# 4. 2か所が見えていることを確認する
git remote -v
#   origin    … 自分のfork（push する先）
#   upstream  … 本家（最新を取ってくる先）

# 5. 依存を入れる
npm install
npx playwright install chromium
```

> **`git fetch origin` では本家の更新は届きません。** `origin` が指しているのは自分のforkです。
> 本家の修正を取り込むときは、必ず `upstream` のほうを使ってください。

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

> なお、Day課題の `playwright-training` は Fork ではありません。あちらは全員に書き込み権限があるので
> `git fetch origin` で正しく、`upstream` は要りません。**Forkするのはこのリポジトリだけ**です。

## テストの実行

### 練習問題（すべて `test.fixme` でスキップ状態）

```bash
npx playwright test
```

各問題は `test.fixme(...)` になっています。`test(...)` に書き換えて中身を埋めると実行されます。

### 模範解答の実行

```bash
npm run test:answers
```

## 構成

| ディレクトリ | 内容 |
|---|---|
| `pages/` | POM ファイル（`LoginPage` `StatsPage` は完成済み。`BookPage` `BookModal` は TODO あり） |
| `tests/` | 練習問題（15問 × 3ファイル = 全45問） |
| `answers/` | 模範解答（テスト3ファイル ＋ `answers/pages/` にPOM 4ファイル） |

## 練習内容

1. **practice1_aaa** — AAA パターンで `StatsPage` のテストを書く（POM は完成済み）
2. **practice2_edit_pom** — `BookPage` / `BookModal` の TODO を埋めてテストを書く
3. **practice3_new_pom** — `DisplayPage` / `EmailPage` の POM を新規作成してテストを書く

この順に進めると、無理なく難易度が上がります。

## 模範解答を見るタイミング

`answers/` に模範解答が入っていますが、**自分で書いて Pull Request を出したあとに見てください。**
先に見ると「読めば分かる」で終わってしまい、書けるようにはなりません。
30分考えて手が止まったときは、解答を見るよりOJT担当者に声をかけた方が早く進みます。

## 提出のしかた

`main` に直接コミットせず、ブランチを切って Pull Request を出してください。
**ブランチは、必ず本家の最新（`upstream/main`）から切ります。**
自分のforkの `main` は、Forkした日のまま止まっていることがあるためです。

```bash
git fetch upstream
git checkout -b taro-tanaka-practice1 upstream/main   # 名前 ＋ 何番の練習か

# 中身を書く

npx playwright test                                   # passed を見てから出す
git add .
git commit -m "practice1: AAAパターンでStatsPageのテストを実装"
git push origin taro-tanaka-practice1
```

そのあとGitHubで Pull Request を作ります。

- base は **本家（`ryujikawakami-jpg`）の `main`** ← compare は自分のブランチ
- PRの説明に「どの問題を解いたか」「詰まった箇所」を書いておくと、レビューが早く終わります

提出したら、SkillCheck の「模擬試験 → Playwright社内試験 → 実技模擬」のページで
**「PRを提出した」**を押しておいてください。OJT担当者がレビューに入りやすくなります。

## うまく動かないとき

| 症状 | 原因 | 直し方 |
|---|---|---|
| ログインで止まって、1本も通らない | forkが古く、練習サイトのURLが変わる前のまま | `git fetch upstream && git merge upstream/main` |
| `git fetch origin` しても本家の修正が入らない | `origin` は自分のfork | `upstream` を足す（上記「最初にやること」の3） |
| Pull Request の base が自分のforkになっている | forkからforkへのPRになっている | base を本家の `main` に選び直す |
| ブランチにコンフリクトが出た | 本家より古いところから枝を切った | `upstream/main` から切り直して、書いたファイルを移す |
| `Cannot find module '@playwright/test'` | `npm install` をしていない | リポジトリ直下で `npm install` |

## ローカルで動かしたい場合（任意）

別のURLに対して実行したいときは `BASE_URL` を渡します。

```bash
BASE_URL=http://localhost:8080 npx playwright test
```
