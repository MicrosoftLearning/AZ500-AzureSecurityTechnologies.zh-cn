---
lab:
    title: '14 - Microsoft Defender for Cloud'
    module: '模块 04 - Microsoft Defender for Cloud'
---

# 实验室 14：Microsoft Defender for Cloud
# 学生实验室手册

## 实验室场景

你需要创建基于 Microsoft Defender for Cloud 的环境的概念证明。具体来说，你需要：

- 配置 Microsoft Defender for Cloud，以监视虚拟机。
- 查看适用于虚拟机的 Microsoft Defender for Cloud 建议。
- 实现有关来宾配置和实时 VM 访问的建议。 
- 查看如何使用安全功能分数来确定创建更安全的基础结构的进度。

> 对于本实验室中的所有资源，我们使用“**美国东部**”区域。请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 Microsoft Defender for Cloud

### 练习 1：实现 Microsoft Defender for Cloud

在该练习中，你将完成以下任务：

- 任务 1：配置 Microsoft Defender for Cloud
- 任务 2：查看 Microsoft Defender for Cloud 建议
- 任务 3：实现 Microsoft Defender for Cloud 建议，启用实时 VM 访问

#### 任务 1：配置 Microsoft Defender for Cloud

在此任务中，你将载入并配置 Microsoft Defender for Cloud。

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 在 Azure 门户页面顶部的“**搜索资源、服务和文档**”文本框中，键入“**Microsoft Defender for Cloud**”，然后按 **Enter** 键。

1. 在“**Microsoft Defender for Cloud \| 开始**”选项卡上，单击“**升级**”。
     
1. 在“**Microsoft Defender for Cloud \| 开始**”选项卡上的“**安装代理**”选项卡中，向下滚动并单击“**安装代理**”。

1. 在“**Microsoft Defender for Cloud \| 开始**”选项卡上，在“**升级**”选项卡 > 选择具有增强安全功能的工作区 > 启用“**Microsoft Defender 计划**”。 

    >**备注**：查看作为 Microsoft Defender 计划的一部分提供的所有功能。 

1. 在“**Defender 计划**”边栏选项卡上，选择“**启用所有 Microsoft Defender for Cloud 计划**”，然后单击“**保存**”。

1. 在“**设置 \| Azure Defender 计划**”边栏选项卡，选择“**全部启用**”，然后单击“**保存**”。

1. 在“**Microsoft Defender for Cloud | 环境设置**”边栏选项卡上，单击相关订阅。 

1. 在“**设置 | Defender 计划**”边栏选项卡上的左侧垂直菜单中，单击“**自动预配**”。

1. 在“**设置 \| 自动预配**”边栏选项卡上，在左侧的垂直菜单中，单击“**工作流自动化**”。

1. 在“**设置 \| 工作流自动化**”边栏选项卡上，单击“**+ 添加工作流自动化**”。

1. 在“**添加工作流程自动化**”边栏选项卡中查看可用的设置。 

    >**备注**：你可以触发基于操作的威胁检测警报和 Microsoft Defender for Cloud 建议。你还可以基于逻辑应用配置操作。 

1. 在“**添加工作流程自动化**”边栏选项卡，单击“**取消**”。

    >**备注**：通过 Microsoft Defender for Cloud 可以更好地了解虚拟机，包括系统更新状态、操作系统安全配置和终结点保护。

1. 导航回“**Microsoft Defender for Cloud**”边栏选项卡，然后单击表示你在上一个实验室中创建的 Log Analytics 工作区的条目。

1. 在“**设置 \| Azure Defender 计划**”边栏选项卡上，确保已选择“**启用 Azure Defender**”，然后单击“**保存**”。


#### 任务 2：查看 Microsoft Defender for Cloud 建议

在此任务中，你将查看 Microsoft Defender for Cloud 建议。 

1. 在 Azure 门户中，导航回“**Microsoft Defender for Cloud \| 概述**”边栏选项卡。 

1. 在“**Microsoft Defender for Cloud \| 概述**”边栏选项卡上，查看“**安全功能分数**”磁贴。

    >**备注**：记录当前分数（如果有）。

1. 导航回“**Microsoft Defender for Cloud \| 概述**”边栏选项卡，选择“**已评估的资源**”。

1. 在“**清单**”边栏选项卡上，选择“**myVM**”条目。

    >**备注**：可能需要等待几分钟，然后刷新浏览器页面，该条目才会出现。
    
1. 在“**资源运行状况**”边栏选项卡的“**建议**”选项卡上，查看针对 **myVM** 的建议列表。


#### 任务 3：实现 Microsoft Defender for Cloud 建议，启用实时 VM 访问

在此任务中，你将实现 Microsoft Defender for Cloud 建议，在虚拟机上启用实时 VM 访问。 

1. 在 Azure 门户中，导航回“**Microsoft Defender for Cloud \| 概述**”边栏选项卡，然后选择“**云安全**”磁贴下的“**工作负载保护**”。

1. 在“**工作负载保护**”边栏选项卡的“**高级防护**”部分，单击“**实时 VM 访问**”磁贴，然后在“**实时 VM 访问**”边栏选项卡上单击“**尝试实时 VM 访问**”。

1. 在“**实时 VM 访问**”上，选择“**未配置**”，然后单击“**myVM**”条目。

    >**备注**：你可能需要等待几分钟才能使用 **myVM** 条目。

1. 选择“**在 VM 1 上启用 JIT**”。

1. 在“**JIT VM 访问配置**”边栏选项卡上，在引用端口 **22**的行的最右边，单击省略号按钮，然后单击“**删除**”。

1. 在“**JIT VM 访问配置**”边栏选项卡上，单击“**保存**”。

    >**备注**：可以通过单击工具栏中的“**通知**”图标并查看“**通知**”边栏选项卡来监视配置进度。 

    >**备注**：安全分数需要一些时间才能体现本实验室中的建议实现情况。定期检查安全功能分数，以确定实施这些功能的影响。 

> 结果：你已载入 Microsoft Defender for Cloud 并实现了虚拟机建议。 


>**备注**：不要删除该实验室中的资源，因为 Azure Sentinel 实验室需要这些资源。
