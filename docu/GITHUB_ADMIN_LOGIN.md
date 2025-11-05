# GitHub 組織管理者としてログインする方法

## 📋 現在の状況

- **GitHubアカウント**: `SAMEO-jp`
- **GitHub CLI**: インストール済み・認証済み
- **認証状態**: ✓ ログイン済み

## 🔐 組織管理者としてログインする方法

GitHubでは、組織の管理者になるには以下の2つの方法があります：

### 方法1: 既存の組織オーナーから管理者権限を付与される

既存の組織オーナーまたは管理者が、あなたに管理者権限を付与する必要があります。

#### ステップ1: 組織のメンバーになる

1. **組織のページにアクセス**
   ```
   https://github.com/pme-dev-system
   ```

2. **組織のオーナーに連絡**
   - 組織のオーナーまたは既存の管理者に連絡
   - 組織への招待を依頼

3. **招待を受け取る**
   - メールまたはGitHub通知から招待を承認
   - 組織のメンバーになる

#### ステップ2: 管理者権限をリクエスト

1. **組織の設定ページにアクセス**
   - `https://github.com/organizations/pme-dev-system/settings/members`

2. **現在の権限を確認**
   - あなたのアカウントが表示されているか確認
   - 現在の権限レベルを確認（Member / Owner）

3. **管理者権限をリクエスト**
   - 組織のオーナーに連絡して、以下の権限をリクエスト：
     - **Owner（オーナー）**: 組織の完全な管理権限
     - **または Billing manager（課金管理者）**: 組織の課金管理権限

#### ステップ3: 権限が付与されたら確認

```bash
# GitHub CLIで組織のメンバーシップを確認
gh api user/orgs --jq '.[] | select(.login == "pme-dev-system") | {login: .login, role: .role}'

# 組織のメンバー情報を確認
gh api orgs/pme-dev-system/members --jq '.[] | select(.login == "SAMEO-jp")'
```

### 方法2: 自分で組織を作成する（新規組織の場合）

もし `pme-dev-system` がまだ存在しない場合、自分で組織を作成できます：

1. **GitHubにログイン**
   ```
   https://github.com/settings/organizations
   ```

2. **新しい組織を作成**
   - 「New organization」をクリック
   - 組織名: `pme-dev-system`
   - プランを選択（Free / Team / Enterprise）

3. **組織の設定**
   - 組織の説明を追加
   - メンバーを招待（必要に応じて）
   - リポジトリを作成または移行

## 🔍 現在の権限を確認する方法

### GitHub CLIを使用

```bash
# 所属している組織の一覧を確認
gh api user/orgs --jq '.[] | {login: .login, role: .role}'

# 特定の組織のメンバー情報を確認
gh api orgs/pme-dev-system/members --jq '.[] | select(.login == "SAMEO-jp")'

# 組織の詳細情報を確認
gh api orgs/pme-dev-system --jq '{login: .login, description: .description, type: .type}'

# 組織のメンバー一覧を確認（管理者権限が必要）
gh api orgs/pme-dev-system/members --jq '.[] | {login: .login, role: .role, type: .type}'
```

### GitHub Web UIで確認

1. **組織のページにアクセス**
   ```
   https://github.com/pme-dev-system
   ```

2. **設定ページにアクセス**
   ```
   https://github.com/organizations/pme-dev-system/settings/members
   ```

3. **自分の権限を確認**
   - メンバーリストで自分のアカウントを検索
   - 権限レベルを確認

## 🛠️ 管理者権限を取得した後の設定

### リポジトリへのアクセス権限を設定

1. **リポジトリの設定ページにアクセス**
   ```
   https://github.com/pme-dev-system/PmeLifecycleManagement/settings/access
   ```

2. **メンバーまたはチームに権限を付与**
   - 「Collaborators」セクションでユーザーを追加
   - 権限レベルを選択（Read / Write / Admin）

3. **ブランチ保護ルールを設定**
   - Settings → Branches
   - `master`ブランチの保護ルールを設定または解除

### リモートリポジトリにプッシュできるようにする

管理者権限を取得したら、以下のコマンドでプッシュできるようになります：

```bash
# プッシュを試行
git push upstream master

# まだエラーが出る場合は、認証を再設定
gh auth refresh
```

## 📝 チェックリスト

管理者としてログインするためのチェックリスト：

- [ ] `pme-dev-system` 組織のメンバーになっているか確認
- [ ] 組織のオーナーまたは管理者に連絡して権限をリクエスト
- [ ] 権限が付与されたら、GitHub CLIで確認
- [ ] リポジトリへのアクセス権限を確認
- [ ] 必要に応じてブランチ保護ルールを調整
- [ ] プッシュを試行して動作確認

## 🔗 参考リンク

- [GitHub Organizations Documentation](https://docs.github.com/en/organizations)
- [Managing members in organizations](https://docs.github.com/en/organizations/managing-membership-in-your-organization)
- [Repository roles](https://docs.github.com/en/organizations/managing-access-to-your-organizations-repositories/repository-roles-for-an-organization)

