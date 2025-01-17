---
title: 设置 Python 开发环境
titleSuffix: Azure Machine Learning service
description: 了解在使用 Azure 机器学习服务时如何配置开发环境。 本文介绍如何使用 Conda 环境, 如何创建配置文件, 以及如何配置自己的基于云的笔记本服务器、Jupyter 笔记本、Azure Databricks、Azure Notebooks、Ide、代码编辑器和 Data Science Virtual Machine。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: d2f60b496594946e9175ecf5c1948b08c9065b1b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848196"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>配置 Azure 机器学习的开发环境

本文介绍如何将开发环境配置为使用 Azure 机器学习服务。 Azure 机器学习服务与平台无关。 开发环境的唯一硬性要求是 Python 3。 还建议使用独立的环境, 如 Anaconda 或 Virtualenv。

下表显示了本文中所述的每个开发环境以及优点和缺点。

| 环境 | 优点 | 缺点 |
| --- | --- | --- |
| [基于云的笔记本 VM](#notebookvm) | 入门的最简单方法。 整个 SDK 已安装到你的工作区 VM 中, 笔记本教程已预克隆并可随时运行。 | 缺乏对开发环境和依赖项的控制。 Linux VM 产生的额外成本 (VM 可以在不使用时停止, 以避免收费)。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 |
| [本地环境](#local) | 完全控制开发环境和依赖项。 在您选择的任何生成工具、环境或 IDE 中运行。 | 需要更长的时间才能开始。 必须安装必需的 SDK 包, 并且还必须安装环境 (如果尚未安装)。 |
| [Azure Databricks](#aml-databricks) | 适用于在可缩放的 Apache Spark 平台上运行大规模密集型机器学习工作流。 | 用于试验机器学习或小规模试验和工作流的多余。 Azure Databricks 产生的额外成本。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/databricks/)。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | 类似于基于云的笔记本 VM (已预装 Python, 并且已预装 SDK), 但预安装了其他热门数据科学和机器学习工具。 易于缩放, 并与其他自定义工具和工作流组合。 | 与基于云的笔记本 VM 相比, 较慢的入门体验。 |
| [Azure Notebook](#aznotebooks) | 具有 Python 和 SDK 预装的免费和轻型入门体验。 | 与基于云的笔记本 VM 相比, 可提供更强大的 VM。 独立于工作区和其他资源。 |

本文还提供了以下工具的其他使用技巧:

* [Jupyter Notebook](#jupyter)：如果你已在使用 Jupyter Notebook，SDK 中包含了需要安装的某些附加功能。

* [Visual Studio Code](#vscode)：如果使用 Visual Studio Code，其中包含了一些可以安装的有用扩展。

## <a name="prerequisites"></a>先决条件

Azure 机器学习服务工作区。 若要创建工作区, 请参阅[创建 Azure 机器学习服务工作区](how-to-manage-workspace.md)。 工作区是您开始使用自己的[基于云的笔记本服务器](#notebookvm)、 [DSVM](#dsvm)、 [Azure Databricks](#aml-databricks)或[Azure Notebooks](#aznotebooks)所需的所有工作区。

若要为[本地计算机](#local)安装 SDK 环境, 请[Jupyter Notebook 服务器](#jupyter)或[Visual Studio Code](#vscode)你还需要:

- [Anaconda](https://www.anaconda.com/download/)或[miniconda)](https://conda.io/miniconda.html)包管理器。

- 在 Linux 或 macOS 上，需要 bash shell。

    > [!TIP]
    > 如果在 Linux 或 macOS 上操作，并使用除 bash 以外的 shell（例如 zsh），则在运行某些命令时可能会收到错误消息。 若要解决此问题，请使用 `bash` 命令启动新的 bash shell，然后运行命令。

- 在 Windows 上，需要命令提示符或 Anaconda 提示符（由 Anaconda 和 Miniconda 安装）。

## <a id="notebookvm"></a>你自己的基于云的笔记本 VM

笔记本虚拟机 (预览版) 是一种基于云的安全 Azure 工作站, 它向数据科学家提供 Jupyter 笔记本服务器、JupyterLab 和完全准备好的 ML 环境。

笔记本 VM 为:

+ **安全**。 由于 VM 和笔记本访问权限在默认情况下使用 HTTPS 和 Azure Active Directory 进行保护, 因此 IT 专业人员可以轻松地强制执行单一登录和其他安全功能, 例如多重身份验证。

+ **预配置**。 此完全准备好的 Python ML 环境从常用 IaaS Data Science VM 中提取其 pedigree, 其中包括:
  + Azure ML Python SDK (最新版本)
  + 用于工作区的自动配置
  + Jupyter 笔记本服务器
  + JupyterLab 笔记本 IDE
  + 预先配置的 GPU 驱动程序
  + 深度学习框架的选择


  如果你使用的是代码, VM 将包括教程和示例, 以帮助你了解如何使用 Azure 机器学习服务。 示例笔记本存储在工作区的 Azure Blob 存储帐户中, 使其可在 Vm 之间共享。 运行时, 它们还可以访问工作区的数据存储和计算资源。

+ **简单安装**:随时从 Azure 机器学习工作区中创建一个。 仅提供名称并指定 Azure VM 类型。 请通过本[教程立即试用:设置环境和工作](tutorial-1st-experiment-sdk-setup.md)区。

+ **可自定义**。 尽管提供了托管安全的 VM 产品/服务, 但仍保留了对硬件功能的完全访问权限, 并对其进行自定义以满足你的需求。 例如, 快速创建最新的 NVidia V100 支持的 VM, 以执行 novel 神经网络体系结构的分步调试。

若要停止产生笔记本 VM 费用, 请[停止笔记本 vm](tutorial-1st-experiment-sdk-setup.md#stop-the-notebook-vm)。 

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM 是自定义的虚拟机 (VM) 映像。 它专为数据科学工作而设计，其中预配置了：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 和 Azure 机器学习 SDK 等包
  - Spark Standalone 和 Drill 等常用数据科学工具
  - Azure CLI、AzCopy 和存储资源管理器等 Azure 工具
  - Visual Studio Code 和 PyCharm 等集成开发环境 (IDE)
  - Jupyter Notebook 服务器

Azure 机器学习 SDK 适用于 Ubuntu 或 Windows 版本的 DSVM。 但是，如果还计划将 DSVM 用作计算目标，则仅支持 Ubuntu。

若要使用 DSVM 作为开发环境，请执行以下操作：

1. 在以下任一环境中创建 DSVM：

    * Azure 门户：

        * [创建 Ubuntu Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [创建 Windows Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI：

        > [!IMPORTANT]
        > * 使用 Azure CLI 时，必须先使用 `az login` 命令登录到 Azure 订阅。
        >
        > * 在此步骤中使用命令时，必须提供资源组名称、VM名称、用户名和密码。

        * 若要创建 Ubuntu Data Science Virtual Machine，请使用以下命令：

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * 若要创建 Windows Data Science Virtual Machine，请使用以下命令：

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. DSVM 上已安装 Azure 机器学习 SDK。 若要使用包含此 SDK 的 Conda 环境，请使用以下某个命令：

    * 对于 Ubuntu DSVM：

        ```shell
        conda activate py36
        ```

    * 对于 Windows DSVM：

        ```shell
        conda activate AzureML
        ```

1. 若要验证是否可以访问 SDK 并检查版本，请使用以下 Python 代码：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要将 DSVM 配置为使用你的 Azure 机器学习服务工作区，请参阅[创建工作区配置文件](#workspace)部分。

有关详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本地计算机

当你使用本地计算机 (也可能是远程虚拟机) 时, 请通过执行以下操作来创建 Anaconda 环境并安装 SDK:

1. 下载并安装[Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 版) (如果尚未安装)。

1. 打开 Anaconda 提示符, 并使用以下命令创建环境:

    运行以下命令以创建环境。

    ```shell
    conda create -n myenv python=3.6.5
    ```

    然后激活环境。

    ```shell
    conda activate myenv
    ```

    此示例使用 python 3.6.5 创建一个环境, 但可以选择任何特定的 subversions。 对于某些主要版本 (建议使用 3.5 +), SDK 兼容性可能不会得到保证, 如果遇到错误, 建议在 Anaconda 环境中尝试不同版本。 下载组件和包时，创建环境需要花费几分钟。

1. 在新环境中运行以下命令, 以启用环境特定的 ipython 内核。 当在 Anaconda 环境中使用 Jupyter 笔记本时, 这将确保预期内核和包导入行为:

    ```shell
    conda install notebook ipykernel
    ```

    然后运行以下命令以创建内核:

    ```shell
    ipython kernel install --user
    ```

1. 使用以下命令安装包:

    此命令安装包含笔记本和 automl 的基本 Azure 机器学习 SDK。 额外`automl`的是大型安装, 如果不打算运行自动机器学习试验, 则可以将其从方括号中删除。 另外`automl` , 默认情况下还包括 Azure 机器学习数据准备 SDK 作为依赖项。

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > 如果有消息指出无法卸载 PyYAML，请改用以下命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`

   安装 SDK 需要几分钟时间。 有关安装选项的详细信息, 请参阅[安装指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

1. 为机器学习试验安装其他程序包。

    使用以下任一命令并将 *\<新包 >* 替换为要安装的包。 通过`conda install`安装包需要包是当前通道的一部分 (可在 Anaconda 云中添加新的通道)。

    ```shell
    conda install <new package>
    ```

    或者, 你可以通过`pip`安装包。

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 项目](https://jupyter.org/)的一部分。 它们提供交互式编码体验，可用于创建将实时代码与叙述性文本和图形混合在一起的文档。 Jupyter Notebook 也是与他人共享结果的好方法，因为可用于将代码部分的输出保存在文档中。 可以在各种平台上安装 Jupyter Notebook。

[本地计算机](#local)部分中的过程在 Anaconda 环境中安装了用于运行 Jupyter 笔记本的必需组件。 若要在 Jupyter Notebook 环境中启用这些组件，请执行以下操作：

1. 打开 Anaconda 提示符并激活您的环境。

    ```shell
    conda activate myenv
    ```
    
1. 克隆适用于一组示例笔记本的[GitHub 存储库](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 通过以下命令启动 Jupyter Notebook 服务器:

    ```shell
    jupyter notebook
    ```

1. 若要验证 Jupyter Notebook 是否可以使用 SDK, 请创建**新**笔记本, 选择 " **Python 3** " 作为内核, 然后在笔记本单元中运行以下命令:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 如果在导入模块和接收`ModuleNotFoundError`时遇到问题, 请通过在笔记本单元中运行以下代码, 确保 Jupyter 内核已连接到你的环境的正确路径。

    ```python
    import sys
    sys.path
    ```
    
1. 若要将 Jupyter Notebook 配置为使用你的 Azure 机器学习服务工作区，请转到[创建工作区配置文件](#workspace)部分。


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是跨平台代码编辑器。 它依赖于本地 Python 3 和 Python 支持的 Conda 安装，但它提供用于 AI 的其他工具。 它还支持从代码编辑器中选择 Conda 环境。

若要使用 Visual Studio Code 进行开发，请执行以下操作：

1. 若要了解如何使用 Visual Studio Code 进行 Python 开发，请参阅 [VSCode 中的 Python 入门](https://code.visualstudio.com/docs/python/python-tutorial)。

1. 若要选择 Conda 环境，请打开 VS Code，然后按 Ctrl-Shift-P（Linux 和 Windows）或 Command-Shift-P (Mac)。
    此时会打开“命令面板”。

1. 输入 Python:__Select Interpreter__，然后选择 Conda 环境。

1. 若要验证是否可以使用 SDK，请创建并运行包含以下代码的新 Python 文件 (.py)。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要安装适用于 Visual Studio Code 的 Azure 机器学习扩展，请参阅 [AI 工具](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    有关详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure 机器学习](how-to-vscode-tools.md)。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它通过基于 CPU 或 GPU 的计算群集提供基于笔记本的协作环境。

Azure Databricks 如何与 Azure 机器学习服务一起使用:
+ 您可以使用 Spark MLlib 来训练模型, 并从 Azure Databricks 内将模型部署到 ACI/AKS。
+ 你还可以在具有 Azure Databricks 的特殊 Azure ML SDK 中使用[自动机器学习](concept-automated-ml.md)功能。
+ 您可以使用 Azure Databricks 作为[Azure 机器学习管道](concept-ml-pipelines.md)中的计算目标。

### <a name="set-up-your-databricks-cluster"></a>设置 Databricks 群集

创建[Databricks 群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 某些设置仅适用于在 Databricks 上安装用于自动机器学习的 SDK。
**创建群集需要几分钟时间。**

使用以下设置:

| 设置 |适用对象| ReplTest1 |
|----|---|---|
| 群集名称 |始终| yourclustername |
| Databricks 运行时 |始终| 任何非机器学习运行时（非机器学习 4.x、5.x） |
| Python 版本 |始终| 3 |
| 辅助程序 |始终| 2 个或以上 |
| 工作节点 VM 类型 <br>(确定并发迭代的最大数目) |自动化机器学习<br>仅供参考| 首选内存优化的 VM |
| 启用自动缩放 |自动化机器学习<br>仅供参考| 取消选中 |

请等待群集运行完成，然后继续操作。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>将正确的 SDK 安装到 Databricks 库
群集运行后,[创建一个库](https://docs.databricks.com/user-guide/libraries.html#create-a-library), 将合适的 Azure 机器学习 SDK 包附加到群集。

1. **仅选择一个**选项 (不支持其他 SDK 安装)

   |SDK&nbsp;包&nbsp;附加|Source|PyPi&nbsp;名称&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |对于 Databricks| 上传 Python Egg 或 PyPI | azureml-sdk[databricks]|
   |对于 Databricks-<br> 自动 ML 功能| 上传 Python Egg 或 PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > 不能安装其他 SDK 额外内容。 仅选择上述选项之一 [databricks] 或 [automl_databricks]。

   * 不要选择 "**自动附加到所有群集**"。
   * 选择群集名称旁的 "**附加**"。

1. 监视错误, 直到将状态更改为已**附加**, 这可能需要几分钟时间。  如果此步骤失败, 请检查以下各项:

   尝试通过以下方式重新启动群集:
   1. 在左窗格中，选择“群集”。
   1. 在表中选择你的群集名称。
   1. 在“库”选项卡上，选择“重启”。

   还应考虑:
   + 在 Automl 配置中, 使用 Azure Databricks 时, 请添加以下参数:
       1. ```max_concurrent_iterations```基于群集中的辅助角色节点数。
        2. ```spark_context=sc```基于默认 spark 上下文。
   + 或者, 如果你使用的是旧版本的 SDK, 请从群集的已安装库中取消选中它, 然后移到垃圾桶。 安装新的 SDK 版本并重启群集。 如果完成此操作后出现问题，请分离并重新附加群集。

如果安装成功, 则导入的库应如下所示:

Sdk for Databricks, **_无_** 自动机器![学习 Azure 机器学习 sdk for Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

适用于 Databricks的 SDK 与自动![机器学习 SDK 一起安装在 Databricks 上的自动机器学习](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>开始浏览

试用:
+ 下载 Azure Databricks/Azure 机器学习 SDK 的[笔记本存档文件](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc), 并将[该存档文件导入](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive)到 Databricks 群集。
  虽然有许多示例笔记本可用, 但**仅[这些示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)适用于 Azure Databricks。**

+ 了解如何[使用 Databricks 作为训练计算创建管道](how-to-create-your-first-pipeline.md)。

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)（预览版）是 Azure 云中的交互式开发环境。 这是开始 Azure 机器学习开发的一种简单方法。

* 已安装 Azure 机器学习 SDK。
* 在 Azure 门户中创建了 Azure 机器学习服务工作区之后，可以单击相应的按钮将 Azure Notebook 环境自动配置为使用该工作区。

使用 [Azure 门户](https://portal.azure.com)完成 Azure Notebooks 入门。  打开工作区, 从 "**概述**" 部分中选择 " **Azure Notebooks 开始**"。

默认情况下，Azure Notebooks 使用限制为 4GB 内存和 1GB 数据的免费服务层级。 不过，可以通过向 Azure Notebooks 项目附加 Data Science Virtual Machine 实例来解除这些限制。 有关详细信息，请参阅[管理和配置 Azure Notebooks 项目 - 计算层](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)。

## <a id="workspace"></a>创建工作区配置文件

工作区配置文件是一个 JSON 文件，用于告知 SDK 如何与 Azure 机器学习服务工作区进行通信。 该文件命名为 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此 JSON 文件必须采用包含 Python 脚本或 Jupyter Notebook 的目录结构。 它可以位于同一个目录中、一个名为*azureml*的子目录或父目录中。

要从代码使用此文件，请使用 `ws=Workspace.from_config()`。 此代码从文件中加载信息，并连接到工作区。

可通过三种方式创建配置文件：

* **使用[write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)** : 编写一个*配置*文件。 该文件含包含工作区的配置信息。 可以下载 *config.json* 或将其复制到其他开发环境。

* **下载文件**:在[Azure 门户](https://ms.portal.azure.com)中，选择工作区的“概览”部分中的“下载 config.json”。

     ![Azure 门户](./media/how-to-configure-environment/configure.png)

* **以编程方式创建文件**：以下代码片段通过提供订阅 ID、资源组和工作区名称连接到工作区。 然后，它将工作区配置保存到文件中：

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    此代码会将配置文件写入*azureml/.config*文件。


## <a name="next-steps"></a>后续步骤

- 在 Azure 机器学习中使用 MNIST 数据集[训练模型](tutorial-train-models-with-aml.md)
- 查看[适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk) 参考文档
- 了解 Azure 机器学习的[数据准备包](https://aka.ms/data-prep-sdk)
