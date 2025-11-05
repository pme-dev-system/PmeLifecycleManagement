# GitHub リポジトリ権限設定ガイド

## 🔒 権限不足の原因

組織リポジトリ（`pme-dev-system/PmeLifecycleManagement`）への直接プッシュが拒否された理由は、以下のいずれかが考えられます：

### 1. 組織メンバーシップの問題
- `pme-dev-system` 組織に参加していない
- 組織のメンバーだが、リポジトリへのアクセス権限がない

### 2. リポジトリ権限の問題
- リポジトリへの書き込み権限（Write）が付与されていない
- ブランチ保護ルールにより、`master`ブランチへの直接プッシュが制限されている

### 3. 認証の問題
- GitHub認証情報が正しく設定されていない
- SSH鍵が組織に登録されていない（SSH使用時）
- パーソナルアクセストークン（PAT）の権限が不足している（HTTPS使用時）

## ✅ 権限を設定する方法

### ステップ1: 組織への参加確認

1. **GitHubにログイン**して、`pme-dev-system` 組織のページを確認
   - URL: `https://github.com/pme-dev-system`

2. **組織のメンバーか確認**
   - 組織のメンバーでない場合、組織のオーナーまたは管理者に招待を依頼

3. **招待を受け取ったら**
   - メールまたはGitHub通知から招待を承認

### ステップ2: リポジトリ権限の確認とリクエスト

1. **リポジトリにアクセス**
   - URL: `https://github.com/pme-dev-system/PmeLifecycleManagement`

2. **現在の権限レベルを確認**
   - リポジトリページ右上の「Settings」をクリック
   - 「Collaborators」セクションで自分の権限を確認

3. **権限が不足している場合**
   - 組織の管理者に連絡して、以下の権限をリクエスト：
     - **最低限**: Write（書き込み）権限
     - **推奨**: Admin（管理）権限（プロジェクトメンテナの場合）

### ステップ3: 認証方法の確認と設定

#### オプションA: HTTPS認証（パーソナルアクセストークン）

1. **パーソナルアクセストークン（PAT）を作成**
   - GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
   - 「Generate new token」をクリック
   - 必要な権限を選択：
     - `repo` (全てのリポジトリへのアクセス)
     - `workflow` (ワークフローへのアクセス、必要に応じて)

2. **リモートURLをHTTPSに設定**
   ```bash
   git remote set-url upstream https://YOUR_TOKEN@github.com/pme-dev-system/PmeLifecycleManagement.git
   ```

   **注意**: この方法はセキュリティ上推奨されません。代わりに認証情報マネージャーを使用してください。

3. **Git認証情報マネージャーを使用（推奨）**
   ```bash
   # GitHub CLIを使用
   gh auth login
   
   # または、Git Credential Managerを使用
   git config --global credential.helper store
   ```

#### オプションB: SSH認証

1. **SSH鍵を生成**（まだ持っていない場合）
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

2. **SSH鍵をGitHubに登録**
   - GitHub → Settings → SSH and GPG keys
   - 「New SSH key」をクリック
   - 公開鍵を登録

3. **リモートURLをSSHに変更**
   ```bash
   git remote set-url upstream git@github.com:pme-dev-system/PmeLifecycleManagement.git
   ```

4. **接続テスト**
   ```bash
   ssh -T git@github.com
   ```

### ステップ4: ブランチ保護ルールの確認

組織のリポジトリでは、`master`ブランチへの直接プッシュが保護されている可能性があります。

1. **ブランチ保護ルールを確認**
   - リポジトリ → Settings → Branches
   - `master`ブランチの保護ルールを確認

2. **保護ルールがある場合の対処**
   - 保護ルールを一時的に無効化（管理者権限が必要）
   - または、フィーチャーブランチを使用してプルリクエストを作成

## 🔄 推奨ワークフロー

組織リポジトリでは、直接プッシュではなく、以下のワークフローを推奨します：

### フィーチャーブランチ + プルリクエスト

```bash
# 1. フィーチャーブランチを作成
git checkout -b feature/機能名

# 2. 変更をコミット
git add .
git commit -m "feat: 機能説明"

# 3. 個人リポジトリまたは組織リポジトリにプッシュ
git push origin feature/機能名
# または
git push upstream feature/機能名

# 4. GitHub上でプルリクエストを作成
# 5. レビュー後にマージ
```

## 🛠️ トラブルシューティング

### エラー: "permission denied"
- 組織メンバーシップを確認
- リポジトリ権限を確認
- 認証情報を確認

### エラー: "branch is protected"
- ブランチ保護ルールを確認
- フィーチャーブランチを使用

### エラー: "authentication failed"
- SSH鍵またはPATを確認
- 認証情報を再設定

## 📞 次のステップ

1. **組織の管理者に連絡**
   - `pme-dev-system` 組織の管理者に連絡し、リポジトリへのアクセス権限をリクエスト

2. **組織の開発フローを確認**
   - 直接プッシュが許可されているか確認
   - プルリクエストの手順を確認

3. **認証方法を設定**
   - SSHまたはHTTPS認証を適切に設定

## 🔗 参考リンク

- [GitHub Organizations Documentation](https://docs.github.com/en/organizations)
- [Managing repository access](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/managing-teams-and-people-with-access-to-your-repository)
- [About branch protection rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)

