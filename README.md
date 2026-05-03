# cm-devops-agent-usage-alarm

DevOps Agentの月間稼働時間をCloudWatchカスタムメトリクスとアラームで監視する仕組みのCloudFormationテンプレートです。

詳細な解説はブログ記事をご覧ください。

- [DevOps Agentの総稼働時間でアラート通知する](https://dev.classmethod.jp/articles/devops-agent-runtime-alert/)

## 構成

```
EventBridge Scheduler --> Lambda --> DevOps Agent API (get-account-usage)
                            |
                            v
                  CloudWatch カスタムメトリクス
                            |
                            v
                  CloudWatch アラーム --> SNS Topic --> Email通知
```

`get-account-usage` API はリージョン単位の使用量を返します。複数リージョンで利用する場合はリージョンごとに本テンプレートをデプロイしてください。

## デプロイ

以下のリンクからデプロイできます。

[![Launch Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/stacks/create/review?templateURL=https://nyank-devio-cfn-templates.s3.ap-northeast-1.amazonaws.com/cm-devops-agent-usage-alarm/template.yaml&stackName=devops-agent-usage-alarm)

## パラメータ

| パラメータ            | 説明                                 | デフォルト           |
| --------------------- | ------------------------------------ | -------------------- |
| `ResourcePrefix`      | 作成するリソースの名前プレフィックス | `devops-agent-usage` |
| `Schedule`            | Lambdaの起動頻度                     | `rate(1 hour)`       |
| `AlarmThresholdHours` | アラームの閾値（時間）               | `160`                |
| `NotificationEmail`   | アラーム通知先メールアドレス         | （入力必須）         |

スタック作成後、`NotificationEmail` で指定したアドレスにAWSからサブスクリプション確認メールが届きます。メール内のリンクをクリックして購読を承認すると通知が有効化されます。
