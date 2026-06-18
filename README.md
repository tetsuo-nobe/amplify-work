# AWS Amplify ハンズオン

## 概要

このハンズオンでは、AWS Amplify を使用して Next.js のアプリケーションをホスティングします。  
Next.js のシンプルなアプリケーションのコードを AWS CodeCommit のリポジトリに保存して AWS Amplify でアプリケーションとしてデプロイする流れを体験します。

## 前提条件

- 各受講者が AWS アカウントにアクセスできること
- 各受講者が CodeCommit、Amplify、CloudShell の操作権限があること
- リージョン: **東京リージョン (ap-northeast-1)** を使用

## 命名規則

複数の受講者が同一の AWS アカウントを共有する場合もあるため、リソース名が重複しないように、ユニークな **ユーザーID** を決めてください。


## 使用するサービス

- AWS CodeCommit
- AWS CloudShell
- AWS Amplify

---

## 手順

### 1. CodeCommit のリポジトリを作成する

1. AWS マネジメントコンソールにサインインします。
2. リージョンが **東京 (ap-northeast-1)** であることを確認します。
3. サービス検索で `codecommit` と入力し、CodeCommit コンソールを開きます。
4. **「リポジトリを作成」** をクリックします。
5. 以下の設定でリポジトリを作成します。
   - リポジトリ名: `next-hello-ts-{自分のID}`（例: `next-hello-ts-user01`）
   - 説明: （任意）例: `Amplify ハンズオン用リポジトリ`
6. **「作成」** をクリックします。

---

### 2. CloudShell でデフォルトブランチ名を変更する

CodeCommit のデフォルトブランチ名に `main` ブランチを使用します。  
CloudShell で以下のコマンドを実行して、デフォルトブランチ名を `main` に変更します。

1. AWS マネジメントコンソールの左下にある **CloudShell** アイコンをクリックして CloudShell を起動します。
2. 以下のコマンドを実行して、デフォルトブランチ名を `main` に設定します。

```bash
git config --global init.defaultBranch main
```

---

### 3. CloudShell で CodeCommit のローカルリポジトリを作成する

1. CloudShell で以下のコマンドを実行して、CodeCommit リポジトリをクローンします。(自分のID) の部分は適切な内容に置き換えてください。

```bash
cd ~
```

```bash
MYID=(自分のID)
```


* 以下のコマンドを実行して、Git のユーザー情報を設定します。

```bash
git config --global user.email "dummy-${MYID}@example.com"
git config --global user.name "${MYID}"
```

* 以下のコマンドを実行して、Git が CodeCommit の認証に AWS CLI の認証情報を使うように設定します。

```bash
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
```


```bash
git clone https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/next-hello-ts-${MYID}
```

3. クローンが成功すると、`next-hello-ts-${MYID}` ディレクトリが作成されます（空のリポジトリの警告が出ますが問題ありません）。

---

### 4. GitHub のリポジトリからソースコードをクローンする

1. CloudShell で以下のコマンドを実行し、GitHub のパブリックリポジトリから Next.js アプリケーションのソースコードをクローンします。

```bash
cd ~
```

```bash
git clone https://github.com/tetsuo-nobe/next-hello-ts.git next-hello-ts-github
```

---

### 5. GitHub のローカルリポジトリの内容を CodeCommit のローカルリポジトリにコピーする

1. CloudShell で以下のコマンドを実行し、GitHub からクローンした Next.js アプリケーションのソースコードのファイルを CodeCommit のローカルリポジトリにコピーします（`.git` ディレクトリは除外）。


```bash
cd ~/next-hello-ts-github
```

```bash
cp -rp $(ls -A | grep -v "^.git$") ~/next-hello-ts-${MYID}/
```

2. ファイルがコピーされたことを確認します。

```bash
ls ~/next-hello-ts-${MYID}
```

---

### 6. CodeCommit のローカルリポジトリをコミットする

1. CloudShell で以下のコマンドを実行し、ファイルをステージングしてコミットします。

```bash
cd ~/next-hello-ts-${MYID}
```

```bash
git add .
git commit -m "Initial commit - Next.js hello app"
```

---

### 7. CodeCommit のローカルリポジトリをプッシュする

1. CloudShell で以下のコマンドを実行し、CodeCommit のリモートリポジトリにプッシュします。

```bash
git push -u origin main
```

2. プッシュが成功したら、CodeCommit コンソールでリポジトリの内容を確認してみましょう。
   - CodeCommit コンソールで `next-hello-ts-${MYID}` リポジトリを開き、ファイルが表示されることを確認します。
   - 確認したら、CloudShell を閉じます。
---

### 8. Amplify ホスティングでアプリケーションを作成する

1. AWS マネジメントコンソールでサービス検索から `amplify` と入力し、**AWS Amplify** コンソールを開きます。
2. **「新しいアプリを作成」** をクリックします。
3. Git プロバイダーの選択で **「CodeCommit」** を選択し、**「次へ」** をクリックします。
4. リポジトリとブランチの設定を行います。
   - リポジトリ: `next-hello-ts-${MYID}`
   - ブランチ: `main`
5. **「次へ」** をクリックします。
6. ビルドの設定を確認します。
   - アプリケーション名: `next-hello-ts-{ユーザー名}`（デフォルトのまま）
   - ビルドの設定は自動検出されます。
5. **「次へ」** をクリックします。
7. **「保存してデプロイ」** をクリックします。
8. デプロイが開始されます。
   - ページのやや下側に表示されている「ブランチ」の「main」にステータスが表示されます。
   - ステータスが「デプロイ済み」になるまで数分待ちます。

---

### 9. Amplify アプリケーションにアクセスする

1. デプロイ完了後に「ドメイン」に表示される URL を確認します。
2. 表示された URL（例: `https://main.xxxxxxxxxx.amplifyapp.com`）をクリックして、アプリケーションにアクセスします。
3. Next.js アプリケーションが正しく表示されることを確認します。


---

## クリーンアップ

ハンズオン終了後、不要な課金を避けるために以下のリソースを削除してください。

### Amplify アプリケーションの削除

1. AWS マネジメントコンソールで **Amplify** コンソールを開きます。
2. 作成したアプリケーション `next-hello-ts-{ユーザー名}` を選択します。
3. 左側メニューの **「アプリケーションの設定」** > **「全般設定」** を選択します。
4. ページ右下の **「アプリの削除」** をクリックします。
5. 確認ダイアログで `削除` と入力し、**「アプリの削除」** をクリックします。

### CodeCommit リポジトリの削除

1. AWS マネジメントコンソールで **CodeCommit** コンソールを開きます。
2. リポジトリ一覧から `next-hello-ts-{ユーザー名}` のラジオボタンを選択します。
3. **「リポジトリの削除」** をクリックします。
4. 確認ダイアログで、必要な文字を入力して「**削除**」をクリックします。

### CloudShell のクリーンアップ（任意）

CloudShell で作成したローカルディレクトリを削除する場合は、以下のコマンドを実行します。

```bash
cd ~
rm -rf next-hello-ts-${MYID}
rm -rf next-hello-ts-github
```

実行後、CloudShell を閉じます。

---

## トラブルシューティング

| 問題 | 対処法 |
|------|--------|
| CloudShell で `git clone` 時に認証エラーが発生する | IAM ユーザーに `AWSCodeCommitPowerUser` ポリシーがアタッチされているか確認してください |
| Amplify のビルドが失敗する | ビルドログを確認し、`package.json` や `next.config.js` が正しくコピーされているか確認してください |
| Amplify でリポジトリが表示されない | リージョンが東京 (ap-northeast-1) になっているか確認してください |
| `git push` でエラーが発生する | `git remote -v` で remote URL が正しいか確認してください |

---

## 参考情報

- [AWS Amplify ドキュメント](https://docs.aws.amazon.com/amplify/)
- [AWS CodeCommit ドキュメント](https://docs.aws.amazon.com/codecommit/)
- [AWS CloudShell ドキュメント](https://docs.aws.amazon.com/cloudshell/)
