<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec">
    <picture>
      <source srcset="assets/openspec_pixel_dark.svg" media="(prefers-color-scheme: dark)">
      <source srcset="assets/openspec_pixel_light.svg" media="(prefers-color-scheme: light)">
      <img src="assets/openspec_pixel_light.svg" alt="OpenSpec logo" height="64">
    </picture>
  </a>
  
</p>
<p align="center">AIコーディングアシスタントのための仕様駆動開発（Spec-driven development）</p>
<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml"><img alt="CI" src="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml/badge.svg" /></a>
  <a href="https://www.npmjs.com/package/@fission-ai/openspec"><img alt="npm version" src="https://img.shields.io/npm/v/@fission-ai/openspec?style=flat-square" /></a>
  <a href="https://nodejs.org/"><img alt="node version" src="https://img.shields.io/node/v/@fission-ai/openspec?style=flat-square" /></a>
  <a href="./LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square" /></a>
  <a href="https://conventionalcommits.org"><img alt="Conventional Commits" src="https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg?style=flat-square" /></a>
  <a href="https://discord.gg/YctCnvvshC"><img alt="Discord" src="https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white&style=flat-square" /></a>
</p>

<p align="center">
  <img src="assets/openspec_dashboard.png" alt="OpenSpec dashboard preview" width="90%">
</p>

<p align="center">
  更新情報は <a href="https://x.com/0xTab">@0xTab on X</a> をフォロー · 質問やサポートは <a href="https://discord.gg/YctCnvvshC">OpenSpec Discord</a> へ
</p>

# OpenSpec

OpenSpec は、人間と AI コーディングアシスタントを仕様駆動のワークフローで整列させ、実装前に何を作るか合意できるようにします。**APIキーは不要です。**

## なぜ OpenSpec なのか？

AI コーディングアシスタントは強力ですが、要件がチャット履歴に埋もれていると予測不可能になります。OpenSpec は軽量な仕様ワークフローを追加し、実装前に意図を固定化することで、決定可能でレビュー可能な出力を実現します。

主な成果:
- 人間と AI の関係者が作業開始前に仕様で合意する
- 提案、タスク、仕様更新を含む構造化された変更フォルダーにより、スコープが明確かつ監査可能になる
- 提案中、進行中、アーカイブ済みの変更を共通で把握できる
- 既に使っている AI ツールと連携：ネイティブのスラッシュコマンドをサポートするツールではそれを利用し、それ以外は AGENTS.md を介して動作する

## OpenSpec の比較（概要）

- **軽量**: シンプルなワークフロー、APIキー不要、最小限のセットアップ
- **Brownfield-first**: 0→1 に限らず既存プロジェクトへの変更でも有効。OpenSpec は真の仕様（source of truth）と提案を分離します：`openspec/specs/`（現行の仕様） と `openspec/changes/`（提案された更新）。これにより機能横断の差分が明確に管理されます。
- **変更追跡**: 提案、タスク、仕様のデルタが一緒に保存され、アーカイブ時に承認済みの更新を仕様へマージできます。
- **spec-kit や Kiro と比較**: これらは新規機能（0→1）に向いていますが、OpenSpec は既存の挙動変更（1→n）、特に複数の仕様にまたがる更新で優れています。

詳細は [How OpenSpec Compares](#how-openspec-compares) を参照してください。

## 仕組み

```
┌────────────────────┐
│ Draft Change       │
│ Proposal           │
└────────┬───────────┘
         │ AI に意図を共有
         ▼
┌────────────────────┐
│ Review & Align     │
│ (仕様やタスクを編集) │◀──── フィードバックループ ──────┐
└────────┬───────────┘                          │
         │ 承認された計画                        │
         ▼                                      │
┌────────────────────┐                          │
│ Implement Tasks    │──────────────────────────┘
│ (AI がコードを作成) │
└────────┬───────────┘
         │ 変更を出荷
         ▼
┌────────────────────┐
│ Archive & Update   │
│ Specs (source)     │
└────────────────────┘

1. 変更提案（change proposal）を作成して反映したい仕様更新を記述します。
2. AI アシスタントと提案をレビューして合意を得ます。
3. 合意した仕様を参照してタスクを実装します。
4. 変更をアーカイブして、承認済みの更新をソース・オブ・トゥルース（`openspec/specs/`）へマージします。
```

## はじめ方

### 対応する AI ツール

#### ネイティブスラッシュコマンド
以下のツールは OpenSpec のコマンドをネイティブにサポートします。OpenSpec 統合を選択してください。

| ツール | コマンド |
|------|----------|
| **Claude Code** | `/openspec:proposal`, `/openspec:apply`, `/openspec:archive` |
| **Cursor** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` |
| **Factory Droid** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` (`.factory/commands/`) |
| **OpenCode** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` |
| **Kilo Code** | `/openspec-proposal.md`, `/openspec-apply.md`, `/openspec-archive.md` (`.kilocode/workflows/`) |
| **Windsurf** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` (`.windsurf/workflows/`) |
| **Codex** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` (global: `~/.codex/prompts`, 自動インストールあり) |
| **GitHub Copilot** | `/openspec-proposal`, `/openspec-apply`, `/openspec-archive` (`.github/prompts/`) |
| **Amazon Q Developer** | `@openspec-proposal`, `@openspec-apply`, `@openspec-archive` (`.amazonq/prompts/`) |

Kilo Code はチームのワークフローを自動検出します。生成されたファイルを `.kilocode/workflows/` に保存し、コマンドパレットから `/openspec-proposal.md`、`/openspec-apply.md`、`/openspec-archive.md` を実行できます。

#### AGENTS.md 対応
これらのツールはプロジェクト内の `openspec/AGENTS.md` を読み取り、自動的にワークフロー指示に従います。詳細は [AGENTS.md の慣習](https://agents.md/) を参照してください。

| ツール |
|-------|
| Amp • Jules • Gemini CLI • その他 |

### インストールと初期化

#### 前提条件
- **Node.js >= 20.19.0** - バージョンは `node --version` で確認してください

#### ステップ1: CLI をグローバルにインストール

```bash
npm install -g @fission-ai/openspec@latest
```

インストール確認:
```bash
openspec --version
```

#### ステップ2: プロジェクトで OpenSpec を初期化

プロジェクトディレクトリに移動:
```bash
cd my-project
```

初期化を実行:
```bash
openspec init
```

**初期化時に行われること:**
- ネイティブ対応ツール（Claude Code、Cursor、OpenCode など）を選択するプロンプトが表示されます。その他のアシスタントは共通の `AGENTS.md` を利用します。
- 選択したツール向けにスラッシュコマンド設定が自動的に行われ、プロジェクトルートに管理された `AGENTS.md` が書き出されます。
- プロジェクトに `openspec/` ディレクトリ構成が作成されます。

**セットアップ後:**
- プライマリの AI ツールは追加設定なしで `/openspec` ワークフローをトリガーできます。
- `openspec list` を実行してセットアップとアクティブな変更を確認してください。
- スラッシュコマンドがすぐに表示されない場合は、アシスタントを再起動してください。スラッシュコマンドは起動時に読み込まれます。

### 最初の変更を作成する

以下は OpenSpec ワークフローの実例です。どの AI ツールでも動作します。ネイティブスラッシュコマンドを持つツールではショートカットが自動で認識されます。

#### 1. 提案（Proposal）を作成
AI に変更提案を作成してもらいます。

```text
You: Create an OpenSpec change proposal for adding profile search filters by role and team
     (Shortcut for tools with slash commands: /openspec:proposal Add profile search filters)

AI:  I'll create an OpenSpec change proposal for profile filters.
     *Scaffolds openspec/changes/add-profile-filters/ with proposal.md, tasks.md, spec deltas.*
```

#### 2. 検証とレビュー
変更が正しく作成されたかを確認し、提案をレビューします。

```bash
$ openspec list                             # 変更フォルダが存在することを確認
$ openspec validate add-profile-filters     # 仕様フォーマットを検証
$ openspec show add-profile-filters         # 提案、タスク、仕様デルタを確認
```

#### 3. 仕様を洗練する
仕様が要件に合うまで反復します。

```text
You: Can you add acceptance criteria for the role and team filters?

AI:  I'll update the spec delta with scenarios for role and team filters.
     *Edits openspec/changes/add-profile-filters/specs/profile/spec.md and tasks.md.*
```

#### 4. 変更を実装する
仕様が整ったら実装を開始します。

```text
You: The specs look good. Let's implement this change.
     (Shortcut for tools with slash commands: /openspec-apply add-profile-filters)

AI:  I'll work through the tasks in the add-profile-filters change.
     *Implements tasks from openspec/changes/add-profile-filters/tasks.md*
     *Marks tasks complete: Task 1.1 ✓, Task 1.2 ✓, Task 2.1 ✓...*
```

#### 5. 完了した変更をアーカイブする
実装が完了したら変更をアーカイブします。

```text
AI:  All tasks are complete. The implementation is ready.

You: Please archive the change
     (Shortcut for tools with slash commands: /openspec-archive add-profile-filters)

AI:  I'll archive the add-profile-filters change.
    *Runs: openspec archive add-profile-filters --yes*
     ✓ Change archived successfully. Specs updated. Ready for the next feature!
```

またはターミナルで実行:
```bash
$ openspec archive add-profile-filters --yes  # 対話なしで完了した変更をアーカイブ
```

**注:** ネイティブスラッシュコマンドを持つツール（Claude Code、Cursor、Codex など）は上記ショートカットを使用できます。他のツールでも自然言語で「OpenSpec 提案を作って」「OpenSpec 変更を適用して」「変更をアーカイブして」と依頼できます。

## コマンドリファレンス

```bash
openspec list               # アクティブな変更フォルダを見る
openspec view               # 仕様と変更の対話型ダッシュボード
openspec show <change>      # 変更の詳細を表示（提案、タスク、仕様更新）
openspec validate <change>  # 仕様の形式と構造をチェック
openspec archive <change> [--yes|-y]   # 完了した変更を archive/ に移動（--yes で非対話）
```

## 例: AI が作成する OpenSpec ファイル

「二要素認証を追加して」と AI に頼むと、次のような構成が作成されます。

```
openspec/
├── specs/
│   └── auth/
│       └── spec.md           # 現在の認証仕様（存在する場合）
└── changes/
    └── add-2fa/              # AI がこのフォルダごと作成
        ├── proposal.md       # 目的と変更内容
        ├── tasks.md          # 実装チェックリスト
        ├── design.md         # 技術的決定（任意）
        └── specs/
            └── auth/
                └── spec.md   # 追加点を示すデルタ
```

### AI が生成する仕様（`openspec/specs/auth/spec.md` に作成される例）:

```markdown
# Auth Specification

## Purpose
Authentication and session management.

## Requirements
### Requirement: User Authentication
The system SHALL issue a JWT on successful login.

#### Scenario: Valid credentials
- WHEN a user submits valid credentials
- THEN a JWT is returned
```

### AI が生成する変更デルタ（`openspec/changes/add-2fa/specs/auth/spec.md` に作成される例）:

```markdown
# Delta for Auth

## ADDED Requirements
### Requirement: Two-Factor Authentication
The system MUST require a second factor during login.

#### Scenario: OTP required
- WHEN a user submits valid credentials
- THEN an OTP challenge is required
```

### AI が生成するタスク（`openspec/changes/add-2fa/tasks.md` に作成される例）:

```markdown
## 1. Database Setup
- [ ] 1.1 Add OTP secret column to users table
- [ ] 1.2 Create OTP verification logs table

## 2. Backend Implementation  
- [ ] 2.1 Add OTP generation endpoint
- [ ] 2.2 Modify login flow to require OTP
- [ ] 2.3 Add OTP verification endpoint

## 3. Frontend Updates
- [ ] 3.1 Create OTP input component
- [ ] 3.2 Update login flow UI
```

**重要:** これらのファイルを手動で作成する必要はありません。AI アシスタントが既存のコードベースと要件に基づいて生成します。

## OpenSpec ファイルの理解

### デルタ形式

デルタは仕様の変更点を示す「パッチ」です。

- **`## ADDED Requirements`** - 新しい機能
- **`## MODIFIED Requirements`** - 変更された振る舞い（完全な更新テキストを含める）
- **`## REMOVED Requirements`** - 廃止された機能

**フォーマット要件:**
- 見出しには `### Requirement: <name>` を使用する
- 各要件には少なくとも 1 つの `#### Scenario:` ブロックが必要
- 要件文では SHALL/MUST を使用する

## OpenSpec の比較

### spec-kit と比較
OpenSpec の二つのフォルダモデル（`openspec/specs/` が現行の仕様、`openspec/changes/` が提案）により状態と差分が分離されます。これにより既存の機能を変更したり、複数の仕様にまたがる変更を管理しやすくなります。spec-kit は 0→1 に強い一方、OpenSpec は進化的変更に強みがあります。

### Kiro.dev と比較
OpenSpec は機能ごとの変更を一つのフォルダにまとめ（`openspec/changes/feature-name/`）、関連する仕様、タスク、設計を追跡しやすくします。Kiro は変更を複数の仕様フォルダに分散させるため、追跡がやや難しくなる場合があります。

### 仕様を使わない場合との比較
仕様がないと、AI は曖昧なプロンプトからコードを生成しがちで、要件を満たさなかったり不要な機能を追加してしまうことがあります。OpenSpec は実装前に期待される振る舞いを合意することで予測可能性を高めます。

## チーム導入

1. **OpenSpec を初期化** – リポジトリ内で `openspec init` を実行します。
2. **新機能から始める** – AI に今後の作業を変更提案としてキャプチャしてもらいます。
3. **段階的に成長させる** – 各変更はアーカイブされ、ドキュメント化された仕様として蓄積されます。
4. **柔軟性を保つ** – チームメンバーは Claude Code、Cursor、または AGENTS.md 対応ツールを使い分けつつ同じ仕様を共有できます。

ツールを切り替えた場合は `openspec update` を実行してエージェント指示とスラッシュコマンドを最新化してください。

## OpenSpec の更新方法

1. **パッケージをアップグレード**
   ```bash
   npm install -g @fission-ai/openspec@latest
   ```
2. **エージェント指示を更新**
   - 各プロジェクト内で `openspec update` を実行して AI 指示を再生成し、最新のスラッシュコマンドを反映させます。

## コントリビュート

- 依存関係をインストール: `pnpm install`
- ビルド: `pnpm run build`
- テスト: `pnpm test`
- ローカルで CLI を開発: `pnpm run dev` または `pnpm run dev:cli`
- Conventional commits（ワンライナー）: `type(scope): subject`

## ライセンス

MIT
