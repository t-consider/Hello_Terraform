# AWS CloudshellでTerraformを始める
# 1. dnf便利ツールを入れる
sudo dnf install -y yum-utils
# 2. HashiCorp（Terraformの開発元）の公式リポジトリをOSに追加
sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
# 3. Terraform本体をインストール
sudo dnf install -y terraform
# 4. 確認
terraform version
# 5. モジュール作成
vim main.tf
```bash
# 1. 宣言部：Terraform本体とAWSプラグインのバージョン指定
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# 2. 接続先（プロバイダー）の指定：東京リージョンへ繋ぐ
provider "aws" {
  region = "ap-northeast-1"
}

# 3. 実体（リソース）の作成：ハローワールド用VPC
resource "aws_vpc" "hello_vpc" {
  cidr_block = "10.0.0.0/24" # 最小限に絞ったCIDRブロック

  tags = {
    Name = "HelloWorld-VPC"
  }
}

```
# 6. Terraform実行準備
terraform init
# 7. Terafform実行
terraform apply
# 8. applyすると作業対象のプレビューが出るので確認する
## 最後の`Enter a value:`はyesを対話式で入力しEnterすれば進む
```bash
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_vpc.hello_vpc will be created
  + resource "aws_vpc" "hello_vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.0.0.0/24"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name" = "HelloWorld-VPC"
        }
      + tags_all                             = {
          + "Name" = "HelloWorld-VPC"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```
# 9. 成功するとcomplete等出て完了する
```bash
aws_vpc.hello_vpc: Creating...
aws_vpc.hello_vpc: Creation complete after 1s [id=vpc-0ecac0f7ef5e8e6c5]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

# 10. 消すときはterraform destroy
terraform destroy
## 削除対象が`-と赤字で表示されるので確認する`
```bash
aws_vpc.hello_vpc: Refreshing state... [id=vpc-0ecac0f7ef5e8e6c5]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_vpc.hello_vpc will be destroyed
  - resource "aws_vpc" "hello_vpc" {
      - arn                                  = "arn:aws:ec2:ap-northeast-1:125474112778:vpc/vpc-0ecac0f7ef5e8e6c5" -> null
      - assign_generated_ipv6_cidr_block     = false -> null
      - cidr_block                           = "10.0.0.0/24" -> null
      - default_network_acl_id               = "acl-0fb9a3f8f641fee9e" -> null
      - default_route_table_id               = "rtb-01a6a725977170ade" -> null
      - default_security_group_id            = "sg-0b934f46818ddd866" -> null
      - dhcp_options_id                      = "dopt-01e05b519c962f06b" -> null
      - enable_dns_hostnames                 = false -> null
      - enable_dns_support                   = true -> null
      - enable_network_address_usage_metrics = false -> null
      - id                                   = "vpc-0ecac0f7ef5e8e6c5" -> null
      - instance_tenancy                     = "default" -> null
      - ipv6_netmask_length                  = 0 -> null
      - main_route_table_id                  = "rtb-01a6a725977170ade" -> null
      - owner_id                             = "125474112778" -> null
      - tags                                 = {
          - "Name" = "HelloWorld-VPC"
        } -> null
      - tags_all                             = {
          - "Name" = "HelloWorld-VPC"
        } -> null
        # (4 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: 
```
## yesを対話入力しEnterする
```bash
aws_vpc.hello_vpc: Destroying... [id=vpc-0ecac0f7ef5e8e6c5]
aws_vpc.hello_vpc: Destruction complete after 1s

Destroy complete! Resources: 1 destroyed.
```

# 以上がTerraformの入門である心せよ