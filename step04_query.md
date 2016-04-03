# このハンズオンについて

- このハンズオンでは、Redshiftのクラスターとそのクラスタに対してクエリを発行するインスタンスの作成を実施します。
- 今回のハンズオンでは、クラスタの運用に関するコマンドは実行しません。（クラスタの作成、簡単なクエリの発行、クラスタの削除を行います。）
- クエリの発行には、「psql」を利用します。本手順では、Amazon Linux上へのインストールと利用方法は説明します。


# 前提条件

## バージョン確認

このハンズオンは以下のバージョンで動作確認を行いました。

```
aws --version
```

```
aws-cli/1.10.17 Python/2.7.10 Linux/4.1.19-24.31.amzn1.x86_64 botocore/1.4.8
```

## 必要な権限

作業にあたっては、以下の権限を有したIAMユーザもしくはIAMロールを利用してください。

- EC2に対するフルコントロール権限
- RedShiftに関するフルコントロール権限
- IAMに関するフルコントロール権限
- S3に関するフルコントロール権限


# 0. 準備

## リージョンを指定

```
export AWS_DEFAULT_REGION='ap-northeast-1'
```

## 資格情報を確認

```
aws configure list
```

```
Name                    Value             Type    Location
----                    -----             ----    --------
profile                <not set>             None    None
access_key     ****************RDPA         iam-role
secret_key     ****************9GA8         iam-role
region           ap-northeast-1              env    AWS_DEFAULT_REGION
```


# クエリを実行

## SELECT



## INSERT



## DELETE



## UPDATE


