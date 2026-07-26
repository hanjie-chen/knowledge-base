# gcloud-command

gcloud init 命令登录你的 gcp 账号，这样子我们就可以进行各种其他的 gcloud 命令了

查看 gcs 中 bucket 的内容

```shell
gcloud storage ls gs://<bucket-name>
```

查看 project 信息

```shell
gcloud projects list
```

筛选 free tier region 的 zone

```shell
# 筛选这三个特定的 Free Tier 区域
gcloud compute zones list --filter="region:(us-west1 us-central1 us-east1)"
```

确认当前登录账号

```shell
gcloud auth list --filter=status:ACTIVE --format='value(account)'
```

创建 project

```shell
gcloud projects create daily-brief-20260726 \
  --name="Daily Brief" \
  --quiet
```

这里：

- `daily-brief-20260726` 是全局唯一、创建后不可修改的 Project ID。
- `Daily Brief` 是显示名称，可以重复，也可以以后修改。
- `--quiet` 表示无需交互确认。

> [!note]
>
> 需要注意的，Project ID 在整个 Google Cloud 全局唯一，而不仅仅是你的账号中唯一，所以不少 project id 已经被占用了，后面得加点东西（比如说一串日期什么的）

查看项目是否关联了 billing 账号

```shell
gcloud billing projects describe daily-brief-20260726 \
  --format='yaml(projectId,billingAccountName,billingEnabled)'
```

判断标准：

- `billingEnabled: false`：没有启用 Billing。
- `billingAccountName: ''`：没有关联结算账号。
