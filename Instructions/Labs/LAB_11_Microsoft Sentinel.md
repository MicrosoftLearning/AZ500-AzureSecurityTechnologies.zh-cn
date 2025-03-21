---
lab:
  title: 11 - Microsoft Sentinel
  module: Module 04 - Configure and manage security monitoring and automation solutions
---

# 实验室 11：Microsoft Sentinel
# 学生实验室手册

## 实验室方案

**注意：** Azure Sentinel 现更名为 Microsoft Sentinel 

你需要创建基于 Microsoft Sentinel 的威胁检测和响应的概念证明。 具体来说，你需要：

- 开始从 Azure 活动和 Microsoft Defender for Cloud 收集数据。
- 添加内置和自定义警报 
- 查看如何使用 playbook 自动对事件做出反应。

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 Microsoft Sentinel

## Microsoft Sentinel 示意图

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/509aa70d-de11-4470-a289-877fbfecbc00)

## Instructions

## 实验室文件：

- \\Allfiles\\Labs\\15\\changeincidentseverity.json

### 练习 1：实现 Microsoft Sentinel

### 预计用时：30 分钟

在本练习中，你将完成以下任务：

- 任务 1：加入 Microsoft Sentinel
- 任务 2：将 Azure 活动连接到 Sentinel
- 任务 3：创建一个使用 Azure 活动数据连接器的规则。 
- 任务 4：创建 Playbook
- 任务 5：创建自定义警报，并将 Playbook 配置为自动响应。
- 任务 6：调用事件并查看关联操作。

#### 任务 1：加入 Microsoft Sentinel

在此任务中，你将加入 Microsoft Sentinel 并连接 Log Analytics 工作区。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Microsoft Sentinel”，然后按 Enter 键。

    >**注意**：如果这是你第一次在 Azure 仪表板中操作 Microsoft Sentinel，请完成以下步骤：在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Microsoft Sentinel”，然后按 Enter 键。 从“服务”视图中选择“Microsoft Sentinel”。
  
3. 在“Microsoft Sentinel”边栏选项卡上，单击“+ 创建”。

4. 在“将 Microsoft Sentinel 添加到工作区”边栏选项卡上，选择你在 Azure Monitor 实验室中创建的 Log Analytics 工作区，然后单击“添加”。

    >**注意**：Microsoft Sentinel 对工作区有非常具体的要求。 例如，不能使用由 Microsoft Defender for Cloud 创建的工作区。 有关更多内容，请参阅[快速入门：加入 Microsoft Sentinel](https://docs.microsoft.com/en-us/microsoft/sentinel/quickstart-onboard)
    
#### 任务 2：配置 Microsoft Sentinel 以使用 Azure 活动数据连接器。 

在此任务中，你将配置 Sentinel 以使用 Azure 活动数据连接器。  

1. 在 Azure 门户中，在“Microsoft Sentinel \| 概述”边栏选项卡上的“内容管理”部分中，单击“内容中心”。

2. 在“Microsoft Sentinel \| 内容中心”边栏选项卡上，查看可用内容的列表。

3. 在搜索栏中键入“Azure”，然后选择表示“Azure 活动”的条目。 查看最右侧的说明，然后单击“安装”。

4. 等待“安装成功”通知。 在左侧导航面板中的“配置”部分中，单击“数据连接器”。

5. 在“Microsoft Sentinel \| 数据连接器”边栏选项卡上，单击“刷新”并查看可用连接器的列表。 选择表示“Azure 活动”连接器的条目（如果需要，使用 \<< 隐藏左侧的菜单栏），查看它在最右侧的说明和状态，然后单击“打开连接器页面”。

6. 在“Azure 活动”边栏选项卡上，“说明”选项卡应处于选中状态，注意“先决条件”并向下滚动到“配置”   。 记下描述连接器更新的信息。 你的订阅从未使用过旧的连接方法，因此可以跳过第 1 步（“**全部断开连接**”按钮将灰显），然后继续第 2 步。

7. 在第 2 步“通过诊断设置新管道来连接订阅”中，查看“启动 Azure Policy 分配向导并按步骤操作”说明，然后单击“启动 Azure Policy 分配向导\>”。

8. 在“将 Azure 活动日志配置为流式传输到指定 Log Analytics 工作区”（“分配策略”页面）的“基本设置”选项卡上，单击“范围(…)”按钮  。 在“**范围**”页中，从下拉订阅列表中选择你的订阅，然后单击页面底部的“**选择**”按钮。

    >**注意**：请勿选择资源组

9. 单击“基本信息”选项卡底部的“下一步”按钮两次，进入“参数”选项卡。在“参数”选项卡上，单击“主要 Log Analytics 工作区(…)”按钮。 在“**主要 Log Analytics 工作区**”页面中，确保已选中你的订阅，并使用“**工作区**”下拉列表选择用于 Sentinel 的 Log Analytics 工作区。 完成操作后，单击页面底部的“选择”按钮。

10. 单击“参数”选项卡底部的“下一步”按钮，进入“修正”选项卡  。在“修正”选项卡上选择“创建修正任务”复选框 。 这将启用“待修正策略”下拉列表中的“将 Azure 活动日志配置为流式传输到指定 Log Analytics 工作区”。 在“系统分配的标识位置”下拉列表中，选择你先前为 Log Analytics 工作区选择的区域（例如美国东部）。

11. 单击“修正”选项卡底部的“下一步”按钮，进入“不合规消息”选项卡  。如果需要，输入一条不合规消息（这是可选操作），然后单击“不合规消息”选项卡底部的“查看 + 创建”按钮 。

12. 单击“创建”按钮。 你应该会看到三条显示成功的状态消息：“成功创建策略分配”、“成功创建角色分配”和“成功创建修正任务”。

    >**注意**：你可以检查显示为钟形图标的通知来验证这三个任务是否成功。

13. 验证“Azure 活动”窗格是否显示“接收的数据”图（可能必须刷新浏览器页面） 。  

    >**注意**：可能需要等待 15 分钟以上，“状态”才会显示“已连接”并且图形显示“数据已接收”。

#### 任务 3：创建一个使用 Azure 活动数据连接器的规则。 

在此任务中，你将查看并创建一个使用 Azure 活动数据连接器的规则。 

1. 在“Microsoft Sentinel \| 配置”边栏选项卡上，单击“分析”。 

2. 在“Microsoft Sentinel \| 分析”边栏选项卡上，单击“规则模板”选项卡。 

    >**注意**：查看你可以创建的规则类型。 每个规则都与一个特定的数据源关联。

3. 在规则模板列表中，在搜索栏窗体中键入“可疑”，然后单击与“Azure 活动”数据源关联的“资源创建或部署的数量可疑”条目。 然后，在显示规则模板属性的窗格中，单击“创建规则”（根据需要滚动到页面右侧）。

    >**注意**：此规则具有中等严重性。 

4. 在“分析规则向导 - 新建计划规则”边栏选项卡的“常规”选项卡上，接受默认设置，然后单击“下一步: 设置规则逻辑 >”。

5. 在“分析规则向导 - 新建计划规则”边栏选项卡的“设置规则逻辑”选项卡上，接受默认设置，然后单击“下一步: 事件设置(预览) >”。

6. 在“分析规则向导 - 新建计划规则”边栏选项卡的“事件设置”选项卡上，接受默认设置，然后单击“下一步: 自动响应 >”。 

    >**注意**：在此处，你可以将作为逻辑应用实现的 Playbook 添加到规则中，以自动修复问题。

7. 在“分析规则向导 - 新建计划规则”边栏选项卡的“自动响应”选项卡上，接受默认设置，然后单击“下一步: 查看并创建 >”。 

8. 在“分析规则向导 - 新建计划规则”边栏选项卡的“查看并创建”选项卡上，单击“保存”。

    >**注意**：你现在有一个可用规则。

#### 任务 4：创建 Playbook

在此任务中，你将创建 Playbook。 安全剧本是由 Microsoft Sentinel 调用以响应警报的任务集合。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“部署自定义模板”，然后按 Enter 键  。

2. 在“自定义部署”边栏选项卡中，单击“在编辑器中构建自己的模板”选项。

3. 在“编辑模板”边栏选项卡上，单击“加载文件”，找到 \\Allfiles\\Labs\\15\\changeincidentseverity.json 文件，然后单击“打开”。

    >**注意**：可以在 [https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) 中找到示例 playbook。

4. 在“编辑模板”边栏选项卡上，单击“保存”。

5. 在“自定义部署”边栏选项卡上，确保已配置以下设置（其他设置保留默认值）：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB131415|
    |位置|**（美国）美国东部**|
    |Playbook 名称|更改事件严重性|
    |用户名|你的电子邮件地址|

6. 依次单击“查看 + 创建”、“创建”。

    >备注：请等待部署完成。

7. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“资源组”，然后按 Enter 键  。

8. 在“资源组”边栏选项卡的资源组列表中，单击“AZ500LAB131415”条目 。

9. 在“AZ500LAB131415”资源组边栏选项卡上，在资源列表中单击表示新建的 Change-Incident-Severity 逻辑应用的条目。

10. 在“Change-Incident-Severity”边栏选项卡上，单击“编辑”。

    >注意：在“逻辑应用设计器”边栏选项卡中，四个 s 中的每一个都显示一条警告。 这意味着，每个连接都需要检查和配置。

11. 在“逻辑应用设计器”边栏选项卡中，单击第一个 s 步骤。

12. 单击“新增”，确保“租户”下拉列表中的条目包含 Azure AD 租户名称，然后单击“登录”。

13. 出现提示时，使用你在本实验室中使用 Azure 订阅具有的所有者或参与者角色的用户帐户进行登录。

14. 单击第二个 s 步骤，在 s 列表中选择第二个条目，它表示你在上一步中创建的项。

15. 对剩下的两个 s 步骤重复之前的步骤。

    >**注意**：确保任何步骤中都没有显示警告。

16. 在“逻辑应用设计器”边栏选项卡上，单击“保存”保存更改。

#### 任务 5：创建自定义警报并将 playbook 配置为自动响应

1. 在 Azure 门户中，导航回“Microsoft Sentinel \| 概述”边栏选项卡。

2. 在“Microsoft Sentinel \| 概述”边栏选项卡上的“配置”部分中，单击“分析”。

3. 在“Microsoft Sentinel \| 分析”边栏选项卡上，单击“+ 创建”，然后在下拉菜单中单击“计划的查询规则”。 

4. 在“分析规则向导 - 新建计划规则”边栏选项卡的“常规”选项卡上，指定以下设置（保留其他设置的默认值）：

    |设置|值|
    |---|---|
    |名称|playbook 演示|
    |策略|**初始访问**|

5. 单击“下一步:设置规则逻辑 >”。

6. 在“分析规则向导 - 新建计划规则”边栏选项卡的“设置规则逻辑”选项卡上，在“规则查询”文本框中，粘贴以下规则查询。 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >注意：此规则会标识即时 VM 访问策略的删除。

    >备注：如果收到分析错误，表明 Intellisense 可能已在查询中添加了值。 确保查询匹配，否则将查询粘贴到记事本中，然后从记事本粘贴到规则查询中。 


7. 在“分析规则向导 - 新建计划规则”边栏选项卡的“设置规则逻辑”选项卡上，在“查询计划”部分，将“查询运行间隔”设置为“5 分钟”。

8. 在“分析规则向导 - 新建计划规则”边栏选项卡的“设置规则逻辑”选项卡上，接受其余设置的默认值，然后单击“下一步: 事件设置 >”。

9. 在“分析规则向导 - 新建计划规则”边栏选项卡的“事件设置”选项卡上，接受默认设置，然后单击“下一步: 自动响应 >”。 

10. 在“分析规则向导 - 新建计划规则”边栏选项卡的“自动响应”选项卡上，在“自动化规则”下，单击“+ 新添”。

11. 在“新建自动化规则”窗口中，在“自动化规则名称”中输入“Run Change-Severity Playbook”，在“触发器”字段下，单击下拉菜单，然后选择“创建警报时”。

12. 在“新建自动化规则”窗口的“操作”下，阅读备注，然后单击“管理 playbook 权限”。 在“管理权限”窗口中，选中之前创建的“资源组 AZ500LAB1314151”旁边的复选框，然后单击“应用”。

13.  在“新建自动化规则”窗口的“操作”下，单击第二个下拉菜单，然后选择“Change-Incident-Severity”逻辑应用。 在“新建自动化规则”窗口中，单击“应用”。

14. 在“分析规则向导 - 新建计划规则”边栏选项卡的“自动响应”选项卡上，单击“下一步: 查看并创建 >”，然后单击“保存”。

    >**注意**：你现在有一个名为 Playbook 演示的新可用规则。 如果发生由规则逻辑识别的事件，则将导致中等严重性警报，并生成相应的事件。

#### 任务 6：调用事件并查看关联操作。

1. 在 Azure 门户中，导航到“Microsoft Defender For Cloud \| 概述”边栏选项卡。

    >**注意**：查看你的安全分数。 现在它应该已经更新了。 

2. 在“Microsoft Defender for Cloud \| 概述”边栏选项卡上，单击左侧导航栏中“云安全”下的“工作负载保护”。

3. 在“Microsoft Defender for Cloud \| 工作负载保护”边栏选项卡上，向下滚动，然后单击“高级保护”下的“即时 VM 访问”磁贴。

4. 在“即时 VM 访问”边栏选项卡上，在引用 myVM 虚拟机的行的右侧，单击省略号 (...) 按钮，单击“删除”，然后单击“是”********************。

    >**备注：** 如果 VM 未在“**实时 VM**”中列出，请导航到“**虚拟机**”边栏选项卡并单击“**配置**”，然后单击“**实时 VM 访问**”下的“**启用实时 VM**”选项。 重复上述步骤，导航回“Microsoft Defender for Cloud”并刷新页面，此时将显示 VM。

5. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“活动日志”，然后按 Enter 键。

6. 导航到“活动日志”边栏选项卡，请注意“删除 JIT 网络访问策略”条目 。 

    >注意：这可能需要几分钟才能出现。 如果页面未显示，请刷新它。

7. 在 Azure 门户中，导航回“Microsoft Sentinel \| 概述”边栏选项卡。

8. 在“Microsoft Sentinel \| 概述”边栏选项卡上，查看仪表板，并验证它是否显示了与删除即时 VM 访问策略相对应的事件。

    >**注意**：警报可能需要 5 分钟的时间才会在“Microsoft Sentinel \| 概述”边栏选项卡中显示。 如果此时没有看到警报，请运行上一个任务中引用的查询规则，以验证即时访问策略删除活动是否已传播到与 Microsoft Sentinel 实例关联的 Log Analytics 工作区中。 如果没有，请重新创建即时 VM 访问策略，然后再次将其删除。

9. 在“Microsoft Sentinel \| 概述”边栏选项卡上的“威胁管理”部分中，单击“事件”。

10. 验证边栏选项卡是否显示严重性级别为中或高的事件。

    >**注意**：事件可能需要 5 分钟的时间才会在“Microsoft Sentinel \| 事件”边栏选项卡中显示。 

    >**注意**：查看“Microsoft Sentinel \| playbook”边栏选项卡。 你会在那里发现成功运行和失败运行的计数。

    >**注意**：你可以选择为事件分配不同的严重性级别和状态。

> 结果：你创建了一个 Microsoft Sentinel 工作区，将其连接到了 Azure 活动日志，创建了一个 playbook 和为响应即时 VM 访问策略的删除而触发的自定义警报，并验证了配置是否有效。

**清理资源**

> 记得删除所有不再使用的新建 Azure 资源。 删除未使用的资源，确保不产生意外成本。 

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 如果出现提示，请单击“PowerShell”和“创建存储”。

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. 关闭 Cloud Shell 窗格。
