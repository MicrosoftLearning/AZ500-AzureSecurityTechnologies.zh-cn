---
lab:
  title: 15 - Microsoft Defender for Cloud
  module: Module 04 - Microsoft Defender for Cloud
---

# 实验室 15：Microsoft Defender for Cloud
# 学生实验室手册

## 实验室方案

你需要创建基于 Microsoft Defender for Cloud 的环境的概念证明。 具体来说，你需要：

- 配置 Microsoft Defender for Cloud 以监视虚拟机。
- 查看适用于虚拟机的 Microsoft Defender for Cloud 建议。
- 实现有关来宾配置和实时 VM 访问的建议。 
- 查看如何使用安全功能分数来确定创建更安全的基础结构的进度。

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 Microsoft Defender for Cloud

## Microsoft Defender for Cloud 示意图

![image](https://user-images.githubusercontent.com/91347931/157537800-94a64b6e-026c-41b2-970e-f8554ce1e0ab.png)

## 说明

### 练习 1：实现 Microsoft Defender for Cloud

在本练习中，你将完成以下任务：

- 任务 1：配置 Microsoft Defender for Cloud
- 任务 2：查看 Microsoft Defender for Cloud 建议
- 任务 3：实施 Microsoft Defender for Cloud 建议，以启用“实时 VM 访问”

#### 任务 1：配置 Microsoft Defender for Cloud

在此任务中，你将载入和配置 Microsoft Defender for Cloud。

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Microsoft Defender for Cloud”，然后按 Enter 键  。

3. 在左侧导航面板中，单击“**开始**”。 在“Microsoft Defender for Cloud \| 入门”边栏选项卡上，单击“升级” 。
     
4. 在“**Microsoft Defender for Cloud \| 开始**”边栏选项卡上，在“安装代理”选项卡中，向下滚动并单击“**安装代理**”。 

5. 在“Microsoft Defender for Cloud \| 入门”边栏选项卡的“升级”选项卡上，向下滚动直到“选择具有增强安全功能的工作区”部分可见 >> 通过选择 Log Analytics 工作区启用 Microsoft Defender 计划，然后单击蓝色的大“升级”按钮   。  

    >**注意**：查看 Microsoft Defender 计划中提供的所有功能。 

6. 导航到“**Microsoft Defender for Cloud**”，然后在左侧导航面板中的“管理”部分下，单击“**环境设置**”。

7. 在“**Microsoft Defender for Cloud \| 环境设置**”边栏选项卡上，向下滚动并展开，直到出现你的订阅，然后单击相关订阅。 

8. 在“**设置 \| Defender 计划**”边栏选项卡上，选择“**启用所有计划**”，并根据需要单击“**保存**”。

9. 导航回“**Microsoft Defender for Cloud \| 环境设置**”边栏选项卡，不断展开，直到你的订阅出现，然后单击表示在上一个实验室中创建的 Log Analytics 工作区的条目。

10. 在“**设置 \| Defender 计划**”边栏选项卡上，确保所有选项均为“打开”。 如果需要，请单击“**启用所有计划**”，然后单击“**保存**”。

11. 从“**设置 \| Defender 计划**”边栏选项卡中选择“**数据收集**”。 依次单击“**所有事件**”和“**保存**”。

#### 任务 2：查看 Microsoft Defender for Cloud 建议

在此任务中，你将查看 Microsoft Defender for Cloud 建议。 

1. 在 Azure 门户中，导航回“Microsoft Defender For Cloud \| 概述”边栏选项卡。 

2. 在“Microsoft Defender For Cloud \| 概述”边栏选项卡上，查看“安全功能分数”磁贴 。

    >**注意**：记录当前分数（如果有）。

3. 导航回“**Microsoft Defender For Cloud \| 概述**”边栏选项卡，单击“**已评估资源**”。

4. 在“**清单**”边栏选项卡上，单击“**myVM**”条目。

    >**注意**：可能需要等待几分钟，然后刷新浏览器页面，该条目才会出现。
    
5. 在“资源运行状况”边栏选项卡的“建议”选项卡上，查看针对 myVM 的建议列表  。

#### 任务 3：实施 Microsoft Defender for Cloud 建议，以启用“实时 VM 访问”

在此任务中，你将实施 Microsoft Defender for Cloud 建议，在虚拟机上启用“实时 VM 访问”。 

1. 在 Azure 门户中，导航回“**Microsoft Defender for Cloud \| 概述**”边栏选项卡，然后单击左侧导航面板中“**云安全**”下的“**工作负载保护**”。

2. 在“**Microsoft Defender for Cloud \| 工作负载保护**”边栏选项卡上，向下滚动到“**高级保护**”部分，然后单击“**实时 VM 访问**”磁贴。

3. 在“**实时 VM 访问**”边栏选项卡上的“**虚拟机**”部分下，选择“**未配置**”，然后选中“**myVM**”条目对应的复选框。

    >**注意**：可能需要等待几分钟，刷新浏览器页，然后再次选择“**未配置**”才会显示该条目。

4. 单击“虚拟机”部分最右侧的“在 1 VM 上启用 JIT”选项。

5. 在“JIT VM 访问配置”边栏选项卡上，在引用端口 22 的行的最右边，单击省略号按钮，然后单击“删除”。

6. 在“JIT VM 访问配置”边栏选项卡上，单击“保存”。

    >**注意**：可以通过单击工具栏中的“通知”图标并查看“通知”边栏选项卡来监视配置进度。 

    >**注意**：安全分数需要一些时间才能体现本实验室中的建议实现情况。 定期检查安全功能分数，以确定实施这些功能的影响。 

> 结果：你已经载入 Microsoft Defender for Cloud 并已实施虚拟机建议。 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.
