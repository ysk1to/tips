# Terraform

## 環境構築

### tfenvをインストール

```
$ brew install tfenv
$ tfenv --version
tfenv 2.0.0
```

### 最新のversionをインストール

```
$ tfenv list-remote
$ tfenv install 0.13.1
$ tfenv use 0.13.1
Switching default version to v0.13.1
Switching completed
```

### vimの開発環境整備

#### hashivim/vim-terraformをインストール
https://github.com/hashivim/vim-terraform

tfファイルを開き、`:LspInstallServer`を実行

### alias

aliasを設定しておくと入力が楽。

```
$ vim .zshrc
# 下記を追記

# terraform
alias tf='terraform'
```

## 基本操作


### リソース変更

#### EC2作成

```
$ mkdir example
$ cd example
$ vim main.tf 
```

```
resource "aws_instance" "example" {
  ami = "ami-0f9ae750e8274075b"
  instance_type = "t3.micro"
}
```

実行に必要なプロバイダ用のバイナリをダウンロード

```
$ tf init
```

実際にEC2を作成する

```
$ tf apply
```

AWSコンソール上からEC2が作成されていることを確認。


### リソース変更

#### apacheを追加

```terraform
resource "aws_instance" "example" {
  ami           = "ami-0f9ae750e8274075b"
  instance_type = "t3.micro"

  tags = {
    Name = "example"
  }

  user_data = <<EOF
    #!/bin/bash
    yum install -y https
    systemctl start httpd.service
EOF
}
```


planを実行するとaws_instance.example must be replacedと出力される

* 既存のリソースを削除して新しいリソースを作成するということ
* 稼働しているとサービスダウンを引き起こす

### 変数

#### variable

```terraform
variable "example_instance_type" {
  default = "t3.micro"
}

resource "aws_instance" "example" {
  ami           = "ami-0f9ae750e8274075b"
  instance_type = var.example_instance_type
}
```

実行時に変数の値を変更できる。

```
$ tf plan -var 'example_instance_type=t3.nano'
```

#### locals

variableでなくlocalsを使うと実行時の変更はできない。（一般的なプログラム変数と同じ使い方）

#### output

出力値を定義できる。

```
resource "aws_instance" "example" {
  ami           = "ami-0f9ae750e8274075b"
  instance_type = "t3.micro"
}

output "example_instance_id" {
  value = aws_instance.example.id
}
```

### 削除

```
$ tf destroy
```

### モジュール

```terraform:http_server/main.tf
variable "instance_type" {}

resource "aws_instance" "default" {
  ami                    = "ami-0f9ae750e8274075b"
  vpc_security_group_ids = [aws_security_group.default.id]
  instance_type          = var.instance_type

  user_data = <<EOF
    #!/bin/bash
    yum install -y httpd
    systemctl start httpd.service
EOF
}

resource "aws_security_group" "default" {
  name = "ec2"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

output "public_dns" {
  value = aws_instance.default.public_dns
}
```

```terraform:main.tf
module "dev_server" {
  source        = "./http_server"
  instance_type = "t3.micro"
}

output "public_dns" {
  value = module.dev_server.public_dns
}
```

モジュールを作成したら下記コマンドいずれかを実行する必要がある

```
$ tf get
$ tf init
```

ファイルレイアウト

Terraformは拡張子「.tf」のファイルを自動的に読み込むため、複数のファイルに分割して実装することが可能。
ただし、モジュールは例外でサブディレクトリ配下に実装する必要がある。

## トラブルシューティング

### regionの入力を求められエラーとなる

```
$ tf plan
provider.aws.region
  The region where AWS operations will take place. Examples
  are us-east-1, us-west-2, etc.

  Enter a value: ap-northeast-1


Error: Missing required argument

The argument "region" is required, but was not set.
```


https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-envvars.html

regionだけ環境変数に設定し動作することを確認（アクセスキー等はcredentialsから読み込まれる）

```
$ export AWS_DEFAULT_REGION=ap-northeast-1
```

もしくはmain.tfにRegion情報を記載しておく。
