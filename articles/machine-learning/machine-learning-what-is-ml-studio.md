---
title: 什么是 Azure 机器学习工作室？| Microsoft Docs
description: 概述 Azure 机器学习工作室：一个拖放式工具，可以通过现成可用的算法库和模块快速构建模型。
keywords: azure 机器学习,azure ml, 机器学习工作室
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/09/2016
ms.author: garye

---
# 什么是 Azure 机器学习工作室？
Microsoft Azure 机器学习工作室是一个协作型拖放式工具，可用于根据数据构建、测试和部署预测分析解决方案。机器学习工作室将模型发布为可让自定义应用或 BI 工具（如 Excel）方便使用的 Web 服务。

机器学习工作室是展开数据科研、执行预测分析、使用云资源和处理数据的中心位置。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 机器学习工作室交互式工作区
若要开发预测分析模型，通常需要使用一个或多个源中的数据，通过各种数据操作和统计函数来转换及分析数据，然后生成一组结果。开发此类模型是一个迭代过程。在修改各种函数及其参数时，结果会不断收敛，直到已训练的有效模型令人满意。

**Azure 机器学习工作室**提供交互式的可视工作区，可在其中轻松构建、测试和迭代预测分析模型。可以将***数据集***和分析***模块***拖放到交互式画布，将它们连接在一起构成***试验***，然后在机器学习工作室中运行。若要迭代模型设计，可以编辑试验，根据需要保存副本，然后重新运行试验。准备就绪后，可以将***训练试验***转换为***预测试验***，然后将其发布为 ***Web 服务***，使其他人可以访问模型。

> [!TIP]
> 若要下载和打印 Microsoft Azure 机器学习工作室功能概述示意图，请参阅 [Overview diagram of Azure Machine Learning Studio capabilities](machine-learning-studio-overview-diagram.md)（Azure Machine 机器学习工作室功能概述）。
> 
> 

不需要编程，只需以可视方式连接数据集和模块，即可构造预测分析模型。

![Azure 机器学习工作室示意图：创建试验、读取多个源的数据、写入评分的数据、写入模型。][ml-studio-overview]

## 机器学习工作室入门
首次进入“机器学习工作室”时，会看到“主页”。[](https://studio.azureml.net)可在此处查看文档、视频、网络研讨会，以及查找其他重要资源。

顶部有三个选项卡：“主页”（开始位置）、“工作室”和“库”。

### 工作室
单击“工作室”选项卡时，系统会要求使用 Microsoft 帐户或者工作帐户或学校帐户登录。登录后，左侧会显示以下选项卡：

* **项目** - 表示单个项目的试验、数据集、笔记本和其他资源的集合
* **试验** - 已创建、运行和保存为草稿的试验
* **WEB 服务** - 已从试验部署的 Web 服务
* **笔记本** - 创建的 Jupyter 笔记本
* **数据集** - 已上载到工作室的数据集
* **训练模型** - 已在试验中训练并保存在工作室中的模型
* **设置** - 用于配置帐户和资源的设置集合。

### 库
单击“库”选项卡将转到“Cortana Intelligence 库”。该库是一个社区，数据科研人员和开发人员可在此共享使用 Cortana Intelligence 套件组件创建的解决方案。

有关该库的详细信息，请参阅 [Share and discover solutions in the Cortana Intelligence Gallery](machine-learning-gallery-how-to-use-contribute-publish.md)（共享和发现 Cortana Intelligence 库中的解决方案）。

## 试验组件
试验由数据集组成，数据集将数据提供给分析模块，将这些模块连接起来即可构成预测分析模型。具体而言，有效的试验有以下特征：

* 试验至少包含一个数据集和一个模块
* 数据集只能连接到模块
* 模块可以连接到数据集或其他模块
* 模块的所有输入端口必须与数据流建立某种连接
* 必须设置每个模块的所有必需参数

可以从头开始创建试验，或者使用现有的示例试验作为模板。有关详细信息，请参阅 [Use sample experiments to create new experiments](machine-learning-sample-experiments.md)（使用示例试验创建新试验）。

有关创建简单试验的示例，请参阅 [Create a simple experiment in Azure Machine Learning Studio](machine-learning-create-experiment.md)（在 Azure 机器学习工作室中创建简单试验）。

有关创建预测分析解决方案的更完整演练，请参阅 [Develop a predictive solution with Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)（使用 Azure 机器学习开发预测解决方案）。

### 数据集
数据集是指已上载到机器学习工作室，可在建模过程中使用的数据。机器学习工作室随附了许多示例数据集供试验，可以根据需要上载更多的数据集。下面是随附数据集的一些例子：

* **各种汽车的 MPG 数据** - 汽车的每加仑燃油英里数 (MPG) 值，按缸数、马力等参数列出。
* **乳腺症数据** - 乳腺癌诊断数据。
* **森林火灾数据** - 葡萄牙东北部森林火灾级别。

构建试验时，可以从画布左侧的数据集列表中选择。

有关机器学习工作室随附的示例数据集列表，请参阅 [Use the sample data sets in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md)（使用 Azure 机器学习工作室中的示例数据集）。

### 模块
模块是可对数据执行的算法。机器学习工作室有许多模块，包括数据引入函数、训练、评分和验证过程。下面是随附模块的一些例子：

* [转换为 ARFF][convert-to-arff] - 将 .NET 序列化数据集转换为属性关系文件格式 (ARFF)。
* [计算基本统计信息][elementary-statistics] - 计算基本统计信息，例如平均值、标准偏差等。
* [线性回归][linear-regression] - 创建在线梯度下降线性回归模型。
* [评分模型][score-model] - 为训练的分类或回归模型评分。

构建试验时，可以从画布左侧的模块列表中选择。

模块可能提供一组参数用于配置模块的内部算法。在画布上选择模块时，模块的参数将显示在画布右侧的“属性”窗格中。可以在该窗格中修改参数来调整模型。

在浏览可用的机器学习算法大型库时如需帮助，请参阅 [How to choose algorithms for Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md)（如何选择 Microsoft Azure 机器学习的算法）。

## 部署预测分析 Web 服务
准备好预测分析模型后，可以从机器学习工作室将它部署为 Web 服务。有关此过程的详细信息，请参阅 [Deploy an Azure Machine Learning web service](machine-learning-publish-a-machine-learning-web-service.md)（部署 Azure 机器学习 Web 服务）。

[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

<!---HONumber=AcomDC_0921_2016-->