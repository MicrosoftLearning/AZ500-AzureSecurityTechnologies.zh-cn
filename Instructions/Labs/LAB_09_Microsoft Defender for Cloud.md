---
lab:
  title: 09 - Microsoft Defender for Cloud
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# 实验室 09：Microsoft Defender for Cloud
# 学生实验室手册

## 实验室方案

你需要创建基于 Microsoft Defender for Cloud 的环境的概念证明。 具体来说，你需要：

- 针对服务器配置 Microsoft Defender for Cloud 增强的安全性功能以监视虚拟机。
- 查看适用于虚拟机的 Microsoft Defender for Cloud 建议。
- 实现有关来宾配置和实时 VM 访问的建议。 
- 查看如何使用安全功能分数来确定创建更安全的基础结构的进度。

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 Microsoft Defender for Cloud

## Microsoft Defender for Cloud 示意图

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## 说明

### 练习 1：实现 Microsoft Defender for Cloud

在本练习中，你将完成以下任务：

- 任务 1：配置 Microsoft Defender for Cloud
- 任务 2：查看 Microsoft Defender for Cloud 建议
- 任务 3：实施 Microsoft Defender for Cloud 建议，以启用“实时 VM 访问”

#### 任务 1：为服务器配置 Microsoft Defender for Cloud 增强安全功能

在此任务中，你将为服务器载入和配置 Microsoft Defender for Cloud 增强安全功能。

1. 启动浏览器会话并登录 [Azure 订阅](https://azure.microsoft.com/en-us/free/?azure-portal=true) 其中您拥有管理权限。

2. 在 Azure 门户中，在 Azure 门户页面顶部的搜索资源、服务和文档文本框中，键入 Microsoft Defender for Cloud，然后按 Enter 键。

3. 在 Microsoft Defender for Cloud 的“管理边栏选项卡”上，转到“环境设置”。 展开环境设置文件夹，直到显示订阅部分，然后单击“订阅”以查看详细信息。

4. 在“设置”边栏选项卡中的“Defender 计划”下，展开云工作负荷保护 (CWP) 。
  
5. 从云工作负荷保护 (CWP) 计划列表中，选择“服务器”。 在页面右侧，将状态从“关”更改为“开”，然后单击“保存”。
  
6. 若要查看 Microsoft Defender for Servers 计划 2 的详细信息，请选择“更改计划 >”。

>**备注**：将云工作负载保护 (CWP) 服务器计划从“关”启用为“开”，可启用 Microsoft Defender for Servers 计划 2。

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
