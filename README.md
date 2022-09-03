# push-to-hatenablog
 GitHubとはてなブログの連携用環境。
 - issueを利用して、記事を作成することができます
 - PullRequestにて変更点を作成し、それがマージされたタイミングではてなブログの記事も更新されます

## セットアップ
### GitHubリポジトリの追加
はてなブログ記事の管理用にGitHubリポジトリを作成してください。

### Secretの追加
GitHubリポジトリに以下の２つのSecretを追加してください。  
GitHubアクションで記事を更新するために使用します。
* `DOMAIN`という名前でSecretを追加してください。値にはブログのドメイン名を設定してください。
* `BSY`という名前で以下のような値のSecretを追加してください。
```yaml
[ブログのドメイン]:\n
  username: [ユーザ名]\n
  password: [AtomPubのAPIキー]\n
default:\n
  local_root: entries
```

※ Secretsの追加は`GitHubリポジトリページ/Settings/Secrets`から設定できます。詳細は以下のページを参照してください。

[暗号化されたシークレットの作成と保存 #暗号化されたシークレットの作成](https://help.github.com/ja/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets#about-encrypted-secrets)

### ローカル環境のセットアップ
#### `blogsync.yaml`の追加
`blogsync.example.yaml`を`blogsync.yaml`に変更して、ドメイン名やユーザ名を書き換えてください。  
ローカル環境から記事を新規追加するために使用します。

`blogsync.yaml`については、以下のページを参照してください。

[x-motemen/blogsync #Configuration](https://github.com/x-motemen/blogsync#configuration)

### `.env、` の追加
.env`ファイルを作成して`DOMAIN=[ブログのドメイン]`を追加してください。

### はてなブログからの記事取得
`npm run pull` で全記事をローカルに取得します。

はてなブログの更新はmainブランチとpull-requestとの差分が対象となります。
記事数が多いとはてなブログの更新に時間がかかるため、mainブランチで記事を取得・コミットして、ブログの更新を回避してください。

特に初回の記事取得など記事数が多い場合は、mainブランチに全記事をコミットしてください。

### 新規記事の投稿
新規記事の投稿は、まず下書き状態のDraftを作成してから
#### Githubのissueを利用した投稿 
新規記事作成用のissueを立て、作成したいエントリのpathを指定します。
issueがcloseされると、はてなブログに下書き記事が作成され、リポジトリにもentryが追加されます。
ただし、 secret に指定したユーザーが著者となるため、寄稿記事としたい場合には、ローカルから自身のID、トークンを使って post してください。

#### ローカルからの記事の投稿
`npm run push -path='パス名'`で記事を下書き状態で新規作成できます

## 編集した記事の更新
記事を編集したブランチを作成し、そのpull-requestがmainへマージされると、差分がGithub Actionsにより、はてなブログで更新されます。

GitHubアクションの設定は以下を確認してください。

[.github/workflows/push.yml](.github/workflows/push.yml)

※ masterブランチからの差分が更新対象となるため、masterブランチで記事ファイルを編集しても記事の更新は実行されません。master以外のブランチで編集してください。


## Slack通知設定
### `.github/workflows/push.yml`の調整
Slackに更新ワークフローの結果を通知する場合は、[.github/workflows/push.yml](.github/workflows/push.yml)内の以下のコメントアウトを解除してください。
```yaml
# - name: notify to slack
#   uses: mm0202/action_slack-notify@master
#   if: always()
#   env:
#     SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Secretの追加
`GitHubリポジトリページ/Settings/Secrets`から以下のSecretを追加してください。
| key | value
| - | - 
| SLACK_WEBHOOK_URL | Incoming Webhookで指定されたWebhook URL









