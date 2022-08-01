---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
ms.openlocfilehash: df6fbcf475fe0a5cefec130ab713f92997dcf206
ms.sourcegitcommit: 022221e69467f2fdccf2e02bb54e6ec395570668
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2022
ms.locfileid: "143988956"
---
# <a name="lab-13-azure-monitor"></a>实验室 13：Azure Monitor
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

你需要创建监视虚拟机性能的概念证明。 具体来说，你需要：

- 配置虚拟机，以便可以收集遥测和日志。
- 显示可以收集哪些遥测和日志。
- 显示如何使用和查询数据。 

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：使用 Azure Monitor 从 Azure 虚拟机收集数据

## <a name="azure-monitor"></a>Azure Monitor

![image](https://user-images.githubusercontent.com/91347931/157536648-0a286514-a7e2-4058-9dea-e42da21eef76.png)

## <a name="instructions"></a>说明

### <a name="exercise-1-collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>练习 1：使用 Azure Monitor 从 Azure 虚拟机收集数据

### <a name="exercise-timing-20-minutes"></a>练习时间：20 分钟

在本练习中，你将完成以下任务： 

- 任务 1：部署 Azure 虚拟机 
- 任务 2：创建 Log Analytics 工作区
- 任务 3：启用 Log Analytics 虚拟机扩展
- 任务 4：收集虚拟机事件和性能数据
- 任务 5：查看和查询收集的数据 

#### <a name="task-1-deploy-an-azure-virtual-machine"></a>任务 1：部署 Azure 虚拟机

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。 如果出现提示，请选择“PowerShell”和“创建存储” 。

3. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

4. 在 Cloud Shell 窗格内的 PowerShell 会话中运行以下命令，创建一个将在本实验室中使用的资源组：
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**注意**：此资源组将用于实验室 13、14 和 15。 

5. 在 Cloud Shell 窗格内的 PowerShell 会话中运行下列命令，创建新的 Azure 虚拟机。 

    >注意：New-AzVm 命令在 Azure CLI 版本 4.24 中不起作用，Microsoft 目前正在调查是否有解决方法。  本实验室中的解决方法是，安装并恢复到不受此问题影响的 Az.Compute 版本 4.23.0。
   
    >说明：恢复到 Az.Compute 版本 4.23.0 
  
   #### <a name="step-1-download-the-working-version-of-the-module-4230-into-your-cloud-shell-session"></a>步骤 1：将模块的工作版本 (4.23.0) 下载到 Cloud Shell 会话中 
   **类型**：Install-Module -Name Az.Compute -Force -RequiredVersion 4.23.0

   #### <a name="step-2-start-a-new-powershell-session-that-will-allow-the-azcompute-assembly-version-to-be-loaded"></a>步骤 2：启动新的 PowerShell 会话，将允许加载 Az.Compute 程序集版本 
   类型：pwsh

   #### <a name="step-3-verify-that-version-4230-is-loaded"></a>步骤 3：验证版本 4.23.0 是否已加载
   **类型**：Get-Module -Name Az.Compute
   
    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

6.  当提示输入凭据时：

    |设置|值|
    |---|---|
    |用户 |localadmin|
    |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >备注：请等待部署完成。 

7. 在 Cloud Shell 窗格中的 PowerShell 会话中运行以下命令，以确认已创建名为“myVM”的虚拟机，且其“ProvisioningState”为“成功”  。

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. 关闭 Cloud Shell 窗格。 

#### <a name="task-2-create-a-log-analytics-workspace"></a>任务 2：创建 Log Analytics 工作区

此任务将创建一个 Log Analytics 工作区。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Log Analytics 工作区”，然后按“Enter”键。

2. 在“Log Analytics 工作区”边栏选项卡上，单击“+ 创建” 。

3. 在“创建 Log Analytics 工作区”边栏选项卡的“基本设置”选项卡上，指定以下设置（其他设置保留默认值） ：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB131415|
    |名称|任何有效的全局唯一名称|
    |区域|**（美国）美国东部**|

4. 选择“查看 + 创建”  。

5. 在“创建 Log Analytics 工作区”边栏选项卡的“查看 + 创建”选项卡中，选择“创建”  。

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>任务 3：启用 Log Analytics 虚拟机扩展

在此任务中，你将启用 Log Analytics 虚拟机扩展。 此扩展在 Windows 和 Linux 虚拟机上安装 Log Analytics 代理。 此代理从虚拟机收集数据，并将其传输到你指定的 Log Analytics 工作区。 安装代理后，它将自动升级，以确保你始终拥有最新的功能和修补程序。 

1. 在 Azure 门户中，导航回“Log Analytics 工作区”边栏选项卡，然后在工作区列表中，单击表示你在上一个任务中创建的工作区的条目。

2. 在“Log Analytics 工作区”边栏选项卡中的“概述”页面上的“连接数据源”部分中，单击“Azure 虚拟机(VM)”条目  。

    >**注意**：为了成功安装代理，虚拟机必须正在运行。

3. 在虚拟机列表中，找到表示你在本练习的第一个任务中部署的 Azure VM myVM 的条目，并注意它被列为“未连接”。

4. 单击“myVM”条目，然后在“myVM”边栏选项卡上单击“连接”。 

5. 等待虚拟机连接到 Log Analytics 工作区。

    >**注意**：这可能需要几分钟的时间。 “myVM”边栏选项卡上显示的“状态”将从“正在连接”变为“此工作区”。 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>任务 4：收集虚拟机事件和性能数据

在此任务中要配置 Windows 系统日志和几个常见性能计数器的集合。 还将查看其他可用资源。

1. 在 Azure 门户中，导航回到在本练习前面创建的 Log Analytics 工作区。

2. 在“Log Analytics 工作区”边栏选项卡上，单击“设置”部分中的“代理配置”。

3. 在“代理配置”边栏选项卡上查看可配置的设置，包括 Windows 事件日志、Windows 性能计数器、Linux 性能计数器、IIS 日志和 Syslog。 

4. 确保选中“Windows 事件日志”，单击“+ 添加 Windows 事件日志”，然后在事件日志类型列表中选择“系统”  。

    >**注意**：这是将事件日志添加到工作区的方式。 其他选择包括“硬件事件”或“密钥管理服务”等。  

5. 取消勾选“信息”复选框，将“错误”和“警告”复选框保留为选中状态  。

6. 单击“Windows 性能计数器”，再单击“+ 添加性能计数器”，查看可用性能计数器的列表，然后添加以下计数器 ：

    - 内存(\*)\可用内存(MB)
    - 处理(\*)\%处理器时间
    - 适用于 Windows 的事件跟踪\总内存使用量 --- 非分页池
    - 适用于 Windows 的事件跟踪\总内存使用量 --- 分页池

    >**注意**：添加计数器并配置为 60 秒的收集采样间隔。
  
7. 在“代理配置”边栏选项卡上，单击“应用”。

#### <a name="task-5-view-and-query-collected-data"></a>任务 5：查看和查询收集的数据

此任务将对数据收集运行日志搜索。 

1. 在 Azure 门户中，导航回到在本练习前面创建的 Log Analytics 工作区。

2. 在“Log Analytics 工作区”边栏选项卡上，单击“常规”部分中的“日志”。

3. 根据需要关闭“欢迎使用日志分析”窗口。 

4. 在“查询”窗格上的“所有查询”列中，向下滚动到资源类型列表的底部，然后单击“虚拟机”  
    
5. 查看预定义查询列表，选择“内存和 CPU 使用情况”，然后单击相应的“运行”按钮 。

    >**注意**：你可以从查询虚拟机可用内存开始。 如果没有任何结果，请检查范围是否设置为虚拟机

6. 该查询将在新的查询选项卡中自动打开。 

    >**注意**：Log Analytics 使用 Kusto 查询语言。 你可以自定义现有查询或创建自己的查询。 

    >**注意**：所选查询的结果将自动显示在查询窗格下方。 要重新运行查询，请单击“运行”。

    >**注意**：由于此虚拟机是刚刚创建的，因此可能还没有任何数据。 

    >**注意**：你可以选择以不同的格式显示数据。 你还可以选择根据查询结果创建警报规则。

    >**注意**：可使用以下步骤在本实验室前面部分部署的 Azure VM 上生成一些额外的负载：

    1. 导航到 Azure VM 边栏选项卡。
    2. 在 Azure VM 边栏选项卡的“操作”部分选择“运行命令”，在“RunPowerShellScript”边栏选项卡上键入以下脚本，然后单击“运行”   ：
    3. 
       ```cmd
       cmd
       :loop
       dir c:\ /s > SWAP
       goto loop
       ```
       
    4. 切换回“Log Analytics”边栏选项卡，并重新运行查询。 可能需要等待几分钟来完成数据收集过程，然后再次重新运行查询。

> 结果：你使用 Log Analytics 工作区配置了数据源和查询日志。 

**清理资源**

>**注意**：请勿删除此实验室中的资源，因为 Azure 安全中心实验室和 Azure Sentinel 实验室需要这些资源。
 
