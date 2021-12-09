## 概要
本記事は、2021年11月1日にGitHubから発表された、GitHub ActionsによるOpenID Connectの概要や実装方法等をまとめた記事になります。
GihHub ActionsでAWSの永続的なクレデンシャル（IDやパスワードをはじめとする、ユーザ等の認証に用いられる情報の総称）を渡すことなく、IAM Roleが利用できるようになったので、その紹介です。

## 前提知識
* GitHub Actions
    * GitHubのCI/CDツール。この記事では、ワークフロー（リポジトリに追加する自動化された手順。 スケジュールまたはイベントによって動く。 ワークフローを使用して、GitHubでプロジェクトをビルド、テスト、パッケージ、リリース、またはデプロイできる。）が分かっていれば読み進められると思います。
    * 詳しい内容は<a href="https://docs.github.com/ja/actions/learn-github-actions">こちら</a>の記事を参考にしてください。
* OAuth
    * 権限譲渡のプロトコル。OAuthによって、ユーザのアクセス権限を様々なアプリに渡すことが出来る。その際は、アクセストークンを発行することでアクセス権限を移譲するため、IDやパスワードなどの情報をアプリ側に渡さずに済む。
    * 詳しい内容は<a href="https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be">こちら</a>の記事を参考にしてください。
* OIDC（OpenID Connect）
    * OAuthを基にして作られた、より安全に認証することが出来る技術。アクセス権を移譲する際に、アクセストークンと同じタイミングでIDトークンも発行する。
    * 詳しい内容は<a href="https://qiita.com/TakahikoKawasaki/items/498ca08bbfcc341691fe">こちら</a>の記事を参考にしてください。

## はじめに
今年の9月頃、以下のツイートが話題になりました。
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Ok I blogged about it. That&#39;s how excited I am. <br><br>1. Deploy this CFN template<br>2. Write this GHA workflow<br>3. Never worry about IAM users again<br> <a href="https://t.co/KJrr2Jw4bE">https://t.co/KJrr2Jw4bE</a> <a href="https://t.co/9IcocgurxP">pic.twitter.com/9IcocgurxP</a></p>&mdash; Aidan W Steele (@__steele) <a href="https://twitter.com/__steele/status/1437984026145427461?ref_src=twsrc%5Etfw">September 15, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

[その後11月に正式にリリースされました。](https://www.publickey1.jp/blog/21/github_actionsopenid_connectgithub.html)
これにより、GitHub ActionsからAWSを利用するときに必要だった、GitHub Actions実行用のアクセスキーが不要になります。
理由は、IAMでIDプロバイダを作り、GitHub側のOIDCで許可することにより、GitHub側からの権限の呼び出しを行えるからです。

詳しい設定内容等は[実装](#実装)を確認してください。

## Github ActionsによるOIDC
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/c60e2428-0cac-13a8-ba5c-fb8a042ac3ed.png)
Github ActionsによるOIDCは上記のダイアグラムのようになっています（[ダイアグラム参照記事](https://docs.github.com/ja/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)）。

① AWSにアクセスするリソースやIAM Roleなどがこれにあたります。
② GitHub Actionsのワークフローが実行されるたびに、GitHub Actionsの環境変数から、OIDCプロバイダーがIDトークンを自動生成します。
③ GitHubのOIDCプロバイダーが自動生成したトークンを基に、AWSリソースへのアクセスをリクエストします。
④ ③のリクエストが問題ない場合、短期間だけ利用できるクラウドアクセストークンをGithub側に返すことで、Github ActionsからAWSリソースへアクセスすることが出来ます。

詳しくは[こちら](https://docs.github.com/ja/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)をご覧ください（英語）。

## 実装

ワークフローを実装するにあたり、

1. リポジトリを作成し
2. 利用するIAMロールを作り
3. ワークフローを作成する

という順番で進めていきます。

### 1. リポジトリの作成
1. Githubで適当にリポジトリを作成します。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/e2cb23d4-0756-3fe2-af3c-ffd44be73d16.png)

### 2. IAM Roleの作成
Github Actionsで使用する、IAM Roleと認証連携のためのIDプロバイダを作成します。

#### 2.1 IDプロバイダの作成
1. IAM>IDプロバイダに画面遷移する
2. 「プロバイダを追加」を押下する
3. プロバイダのタイプを「OpenID Connect」にする
4. プロバイダのURLに`https://token.actions.githubusercontent.com`と入力し、「サムプリントを取得」を押下する
5. 対象者には、`sts.amazonaws.com`と入力する
6. 右下の「プロバイダを追加」を押下する。
<br>
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/796777b0-c9e8-2c37-40cd-7c32f3408c02.png)

#### 2.2 IAM Roleの作成
1. IAM>ロールに画面遷移する
2. 「ロールを作成」を押下する
3. 「信頼されたエンティティの種類を選択」で「ウェブID」を選択する
4. 「IDプロバイダー」のプルダウンで、上記で設定したIDプロバイダを選択する
5. 「Audience」で、上記で設定した「sts.amazonaws.com」を選択する
6. 「次のステップ:アクセス権限」を押下する。
<br>
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/4e52d0f9-1835-c7d0-53ca-53b7c06e861a.png)
7. 「Attachアクセス権限ポリシー」「タグの追加」は特に変更せず次のステップに進む
8. 「確認」画面で任意の名前を入力し、「ロールの作成」を押下する
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/dd383d6d-6675-1d2a-58b5-d678ac753052.png)

特定アカウントやリポジトリに対する設定は[こちら](#注意点)を確認してください。

### 3. ワークフローの作成
今回私は、GithubのGUIでファイルを作成したので、その方法を記載します。

すでにあるリポジトリ内にワークフローを作成する場合は、リポジトリの`.github/workflow`配下に、yml形式のファイルを作成してください。そのファイルに、下記で記載する`oidc_test.yml`のコードを入力して下さい。その後pushすることで、Github Actionsが走ります。

1. 1.で作成したリポジトリの画面で「Actions」画面に遷移する
2. 「Set up this workflow」を押下する
3. ファイル名を任意で入力し、ファイル内に以下のコードを記載する
4. 「Start commit」を押下して、`create oidc_test.yml`などのコミットメッセージを入力し、「Commit new file」を押下する。

```yml:oidc_test.yml

name: OIDC test
on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    steps:
      - run: sleep 5

      - name: Configure AWS
        run: |
          export AWS_ROLE_ARN=arn:aws:iam::XXXXXXXXXXXX:role/github-actions-oidc-test
          export AWS_WEB_IDENTITY_TOKEN_FILE=/tmp/awscreds
          export AWS_DEFAULT_REGION=ap-northeast-1

          echo AWS_WEB_IDENTITY_TOKEN_FILE=$AWS_WEB_IDENTITY_TOKEN_FILE >> $GITHUB_ENV
          echo AWS_ROLE_ARN=$AWS_ROLE_ARN >> $GITHUB_ENV
          echo AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION >> $GITHUB_ENV

          curl -H "Authorization: bearer $ACTIONS_ID_TOKEN_REQUEST_TOKEN" "$ACTIONS_ID_TOKEN_REQUEST_URL&audience=sts.amazonaws.com" | jq -r '.value' > $AWS_WEB_IDENTITY_TOKEN_FILE

      - run: aws sts get-caller-identity
```

ポイントは`permissions: id-token: write`の部分です。これを指定することで、IDトークンの取得に必要な情報が環境変数に設定されます。

`run: sleep 5`の部分は合っても無くても大丈夫です。

`Configure AWS: run`の部分では、IAMロール・トークンファイルパス・リージョンを、Github Actionsの環境変数にセットし、Githubに対してトークンリクエストを投げています。

awscliでは、`AWS_ROLE_ARN`に作成したIAMロールのarnを、`AWS_WEB_IDENTITY_TOKEN_FILE`にトークンファイルのファイルパスをセットしておくことで、トークンファイルを読み込み、設定したIAMロールを`AssumeRoleWithWebIdentity`で呼び出して使用してくれます（[AWS CLIのドキュメント](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-role.html#cli-configure-role-oidc)）。

最後の`curl -H "Authorization: bearer $ACTIONS_ID_TOKEN_REQUEST_TOKEN" "$ACTIONS_ID_TOKEN_REQUEST_URL&audience=sts.amazonaws.com"`で、Githubに対してトークンを発行するためのリクエストを投げています。
[参考にした記事](https://zenn.dev/yutaro1985/articles/b012f69b49bec095b9f1)では、GitHub Actionsが持っている`$ACTIONS_ID_TOKEN_REQUEST_TOKEN`の値を使って`$ACTIONS_ID_TOKEN_REQUEST_URL`にリクエストを送り、一時トークンを発行しているというようなことが記載されていました。

### 4. 動作確認
それでは実際にGithub Actionsを実行してみます。
「Actions」>「OIDC test（ymlファイルに設定したワークフロー名）」>「create oidc_test.yml（コミットメッセージ）」>「build」と遷移すると以下の画面に行きつくと思います。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/ae0b8bed-a9c7-ee91-eb55-3c0b9d05fb97.png)
もし記事の通り実装した場合、ファイルを編集してコミットしたので、すでにワークフローが実行されている状態になります。
緑色のチェックマークが表示されていれば成功です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/98507437-101f-01bf-057e-3459f768c951.png)
マスクしていますが、UserId、Account、Arnに正しい情報が表示されていました。

## 補足

### configure-aws-credentialsライブラリの利用
本記事の[ワークフローの作成](#3-ワークフローの作成)では、一通りの処理の流れを追うために、`run`にコマンドを直書きして実装しました。
現在はコマンド直書きで利用するよりも、`aws-actions/configure-aws-credentials`ライブラリを利用したほうがスッキリするのでその方法も紹介したいと思います。
IDプロバイダやロールは[実装](#22-iam-roleの作成)で作成したままの設定で問題ありません。

```yml:oidc_test.yml
name: AWS Deploy
on: push

env:
  AWS_ROLE_ARN: arn:aws:iam::XXXXXXXXXXXX:role/github-actions-oidc-test

permissions:
  id-token: write
  contents: read
jobs:
  aws-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: aws-actions/configure-aws-credentials@master
        with:
          role-to-assume: ${{ env.AWS_ROLE_ARN }}
          aws-region: ap-northeast-1
      - run: aws sts get-caller-identity
```

この方法では、`env`にロールのarnを設定し、`permissions.id-token`を同じく`write`にすることで、同じようにAssumeRoleし、AWSリソースにアクセスできます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/97298cf5-9bfc-ec5e-a0b5-3607d1bc5cba.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/1422940/ed6ce658-9c3b-a17a-7831-23b2df724db5.png)

成功しました。

### 特定のアカウントやリポジトリに対するロールの設定
[IDプロバイダの作成](#21-idプロバイダの作成)で、対象者に`sts.amazonaws.com`を対象者として設定しました。
この場合、AWSアカウントIDとIAMロール名の二つが分かっている人であれば、誰でもAssumeRoleが可能になってしまいます。

IAM Roleを以下のように設定することで、指定したOrganization、もしくはAccount配下のリポジトリのみで該当ロールを使用することが出来ます。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::XXXXXXXXXXXX:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:${Organization名 or Account名}/*"
        }
      }
    }
  ]
}
```

さらに`repo:${Organization名 or Account名}/${リポジトリ名}:*`と指定すれば、指定したリポジトリ内でしか使用できません（`*`はブランチ名を指定できます）。指定したリポジトリのGithub Actionsで発行したトークンだけが、ロールを引き受けるようにしているため、安全に使用できます。

## まとめ
この機能によって、Github ActionsからセキュアにAWSにアクセスできるようになりました。
OIDCはGithub Actionsに実装された機能なので[GCPでも利用できる](https://ryotarai.hatenablog.com/entry/github-acitons-id-token-gcp)ようです。
参考になれば幸いです。

## 参照
https://dev.classmethod.jp/articles/github-actions-without-permanent-credential/
https://zenn.dev/yutaro1985/articles/b012f69b49bec095b9f1
https://zenn.dev/mryhryki/articles/2021-09-19-access-aws-by-github-actions
https://tech.guitarrapc.com/entry/2021/11/05/025150
