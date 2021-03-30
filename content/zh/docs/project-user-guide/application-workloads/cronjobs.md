---
title: "定时任务"
keywords: "KubeSphere, Kubernetes, 任务, 定时任务"
description: "创建 KubeSphere 定时任务"
linkTitle: "定时任务"

weight: 10260
---

定时任务 (CronJob) 对于创建周期性和重复性任务非常有用，例如运行备份或发送电子邮件。定时任务还可以在特定时间或间隔执行单个任务，例如在集群可能处于空闲状态时执行任务。

有关更多信息，请参见 [Kubernetes 官方文档](https://kubernetes.io/zh/docs/concepts/workloads/controllers/cron-jobs/)。

## 准备工作

您需要创建一个企业空间、一个项目以及一个帐户 (`project-regular`)。必须邀请该帐户至该项目中并赋予 `operator` 角色。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。

## 创建定时任务

### 步骤 1：打开仪表板

以 `project-regular` 身份登录控制台。转到项目的**任务**页面，然后在**定时任务**选项卡下点击**创建**。

![定时任务列表](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/cronjob-list.png)

### 步骤 2：输入基本信息

您可以参考下图在每个字段中输入基本信息。完成操作后，点击**下一步**。

![基本信息](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/cronjob-create-basic-info.png)

- **名称**：定时任务的名称，也是唯一标识符。
- **别名**：定时任务的别名，使资源易于识别。
- **定时计划**：按照给定的时间计划运行任务。语法参照 [CRON](https://zh.wikipedia.org/wiki/Cron)。KubeSphere 中提供了一些预置 CRON 语句以简化输入。该字段由 `.spec.schedule` 指定。对于此定时任务，输入 `*/1 * * * *`，这意味着它每分钟运行一次。

  | 类型       | CRON        |
  | ----------- | ----------- |
  | 每小时  | `0 * * * *` |
  | 每天   | `0 0 * * *` |
  | 每周  | `0 0 * * 0` |
  | 每月 | `0 0 1 * *` |
  
- **高级设置 (执行参数)**:
  
  - **启动 Job 的期限（秒）**：由清单文件中的 `.spec.startingDeadlineSeconds` 指定，此可选字段表示如果由于任何原因错过计划时间，定时任务启动所需的最大秒数。错过执行的定时任务将被计为失败。如果未指定此字段，则此定时任务没有启动期限。
  - **保留完成 Job 数**：由清单文件中的 `.spec.successfulJobsHistoryLimit` 指定，此字段表示要保留的定时任务执行成功的次数，用于区分显式零和未指定这两种情况。默认值为 3。
  - **保留失败 Job 数**：由清单文件中的 `.spec.failedJobsHistoryLimit` 指定，此字段表示要保留的定时任务执行失败的次数，用于区分显式零和未指定这两种情况。默认值为 1。
  - **并发策略**：由 `.spec.concurrencyPolicy` 指定，它表示如何处理任务的并发执行。有效值为：
      - **Allow** (默认值)：允许定时任务并发运行。
      - **Forbid**：禁止并发运行，如果前一个运行还没有完成，则跳过下一个运行。
      - **Replace**：取消当前正在运行的任务，用一个新的来替换。

{{< notice note >}}

您可以在右上角开启**编辑模式**，查看此定时任务的 YAML 格式清单文件。

{{</ notice >}}

### 步骤 3：定时任务设置（可选）

请参考[任务](../jobs/#步骤-3任务设置可选)。

### 步骤 4：设置镜像

1. 点击**容器镜像**下的**添加容器镜像**，在搜索栏中输入 `busybox` 。

    ![输入 busybox](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/input-busybox.png)

2. 向下滚动到**启动命令**然后在**参数**框中输入 `/bin/sh,-c,date; echo "KubeSphere!"`。

    ![启动命令](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/start-command.png)

3. 点击 **√** 完成镜像设置，然后点击**下一步**继续。

    ![完成镜像设置](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/finish-image.png)

    {{< notice note >}}

- 此示例定时任务输出 `KubeSphere`。有关设置镜像的更多信息，请参见[容器镜像设置](../container-image-settings/)。
- 有关**重启策略**的更多信息，请参见[任务](../jobs/#步骤-4设置镜像)。
- 您可以跳过本教程的**挂载存储**和**高级设置**。有关更多信息，请参见部署一文中的[挂载存储卷](../deployments/#步骤-4挂载存储卷)和[配置高级设置](../deployments/#步骤-5配置高级设置)。

    {{</ notice >}}

### 步骤 5：检查结果

1. 在最后一步**高级设置**中，点击**创建**完成操作。如果创建成功，定时任务列表中将添加一个新条目。此外，您还可以在**任务**选项卡下查看任务。

    ![定时任务列表](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/cronjob-list-new.png)

    ![任务列表](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/job-list.png)

2. 在**定时任务**选项卡下，点击此定时任务，然后转到**任务记录**选项卡，您可以在其中查看每个执行记录的信息。由于**保留完成 Job 数**字段设置为 3，因此这里显示定时任务成功执行 3 次。

    ![执行记录](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/execution-record.png)

3. 点击任意记录，您将转到该任务的详情页面。

    ![任务详情页面](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/job-detail-page.png)

4. 在**资源状态**中，您可以检查 Pod 状态。点击右侧的箭头，可以检查容器日志，如下所示，该日志显示预期输出。

    ![容器日志-1](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/container-log-1.png)

    ![容器日志-2](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/container-log-2.png)

## 定时任务操作

在定时任务详情页面上，您可以在创建定时任务之后对其进行管理。

- **编辑信息**：编辑基本信息，但无法编辑该定时任务的`名称`。
- **暂停/启动**：暂停或启动该定时任务。暂停定时任务将告知控制器暂停后续执行任务，但已经启动的执行不受影响。
- **编辑配置文件**：编辑该定时任务的 YAML 文件配置。
- **删除**：删除该定时任务，然后返回定时任务列表页面。

![定时任务操作](/images/docs/zh-cn/project-user-guide/application-workloads/cronjobs/cronjob-action.png)