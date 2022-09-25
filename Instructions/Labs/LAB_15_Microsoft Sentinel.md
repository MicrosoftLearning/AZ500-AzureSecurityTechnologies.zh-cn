---
lab:
  title: 15 - Microsoft Sentinel
  module: Module 04 - Manage Security Operations
---

# <a name="lab-15-microsoft-sentinel"></a>实验室 15：Microsoft Sentinel
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

**注意：** Azure Sentinel 现更名为 Microsoft Sentinel 

You have been asked to create a proof of concept of Microsoft Sentinel-based threat detection and response. Specifically, you want to:

- 开始从 Azure 活动和 Microsoft Defender for Cloud 收集数据。
- 添加内置和自定义警报 
- 查看如何使用 playbook 自动对事件做出反应。

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 Microsoft Sentinel

## <a name="microsoft-sentinel-diagram"></a>Microsoft Sentinel 示意图

![image](https://user-images.githubusercontent.com/91347931/157538440-4953be73-90be-4edd-bd23-b678326ba637.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>实验室文件：

- \\Allfiles\\Labs\\15\\changeincidentseverity.json

### <a name="exercise-1-implement-microsoft-sentinel"></a>练习 1：实现 Microsoft Sentinel

### <a name="estimated-timing-30-minutes"></a>预计用时：30 分钟

在本练习中，你将完成以下任务：

- 任务 1：加入 Microsoft Sentinel
- 任务 2：将 Azure 活动连接到 Sentinel
- 任务 3：创建一个使用 Azure 活动数据连接器的规则。 
- 任务 4：创建 Playbook
- 任务 5：创建自定义警报，并将 Playbook 配置为自动响应。
- 任务 6：调用事件并查看关联操作。

#### <a name="task-1-on-board-azure-sentinel"></a>任务 1：加入 Azure Sentinel

在此任务中，你将加入 Microsoft Sentinel 并连接 Log Analytics 工作区。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Microsoft Sentinel”，然后按 Enter 键。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If this is your first attempt to action Microsoft Sentinel in the Azure dashboard complete the following step(s): In the Azure portal, in the <bpt id="p2">**</bpt>Search resources,  services, and docs<ept id="p2">**</ept> text box at the top of the Azure portal page, type <bpt id="p3">**</bpt>Microsoft Sentinel<ept id="p3">**</ept> and press the <bpt id="p4">**</bpt>Enter<ept id="p4">**</ept> key. Select <bpt id="p1">**</bpt>Microsoft Sentinel<ept id="p1">**</ept> from the <bpt id="p2">**</bpt>Services<ept id="p2">**</ept> view.
  
3. 在“Microsoft Sentinel”边栏选项卡上，单击“+ 创建”。

4. 在“将 Microsoft Sentinel 添加到工作区”边栏选项卡上，选择你在 Azure Monitor 实验室中创建的 Log Analytics 工作区，然后单击“添加”。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Microsoft Sentinel has very specific requirements for workspaces. For example, workspaces created by Microsoft Defender for Cloud can not be used. Read more at <bpt id="p1">[</bpt>Quickstart: On-board Azure Sentinel<ept id="p1">](https://docs.microsoft.com/en-us/azure/sentinel/quickstart-onboard)</ept>
    
#### <a name="task-2-configure-microsoft-sentinel-to-use-the-azure-activity-data-connector"></a>任务 2：配置 Microsoft Sentinel 以使用 Azure 活动数据连接器。 

在此任务中，你将配置 Sentinel 以使用 Azure 活动数据连接器。  

1. 在 Azure 门户中，在“Microsoft Sentinel \| 概述”边栏选项卡上的“配置”部分中，单击“数据连接器”。 

2. 在“Microsoft Sentinel \| 数据连接器”边栏选项卡上，查看可用连接器的列表，在搜索栏中键入“Azure”，选择代表“Azure 活动”连接器的条目（如果需要，可使用“\<<”隐藏左侧的菜单栏），查看其描述和状态，然后单击“打开连接器页面”。

3. On the <bpt id="p1">**</bpt>Azure Activity<ept id="p1">**</ept> blade the <bpt id="p2">**</bpt>Instructions<ept id="p2">**</ept> tab should be selected, note the <bpt id="p3">**</bpt>Prerequisites<ept id="p3">**</ept> and scroll down to the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>. Take note of the information describing the connector update. Your Azure Pass subscription never used the legacy connection method so you can skip step 1 (the <bpt id="p1">**</bpt>Disconnect All<ept id="p1">**</ept> button will be grayed out) and proceed to step 2.

4. 在第 2 步“通过诊断设置新管道来连接订阅”中，查看“启动 Azure Policy 分配向导并按步骤操作”说明，然后单击“启动 Azure Policy 分配向导\>”。

5. On the <bpt id="p1">**</bpt>Configure Azure Activity logs to stream to specified Log Analytics workspace<ept id="p1">**</ept> (Assign Policy page) <bpt id="p2">**</bpt>Basics<ept id="p2">**</ept> tab, click the <bpt id="p3">**</bpt>Scope elipsis (...)<ept id="p3">**</ept> button. In the <bpt id="p1">**</bpt>Scope<ept id="p1">**</ept> page choose your Azure Pass subscription from the drop-down subscription list and click the <bpt id="p2">**</bpt>Select<ept id="p2">**</ept> button at the bottom of the page.

    >**注意**：请勿选择资源组

6. 你需要创建基于 Microsoft Sentinel 的威胁检测和响应的概念证明。

7. 具体来说，你需要：

8. 单击“修正”选项卡底部的“下一步”按钮，进入“不合规消息”选项卡  。如果需要，输入一条不合规消息（这是可选操作），然后单击“不合规消息”选项卡底部的“查看 + 创建”按钮 。

9. Click the <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> button. You should observe three succeeded status messages: <bpt id="p1">**</bpt>Creating policy assignment succeeded, Role Assignments creation succeeded, and Remediation task creation succeeded<ept id="p1">**</ept>.

    >**注意**：你可以检查显示为钟形图标的通知来验证这三个任务是否成功。

10. 验证“Azure 活动”窗格是否显示“接收的数据”图（可能必须刷新浏览器页面） 。  

    >**注意**：可能需要等待 15 分钟以上，“状态”才会显示“已连接”并且图形显示“数据已接收”。

#### <a name="task-3-create-a-rule-that-uses-the-azure-activity-data-connector"></a>任务 3：创建一个使用 Azure 活动数据连接器的规则。 

在此任务中，你将查看并创建一个使用 Azure 活动数据连接器的规则。 

1. 在“Microsoft Sentinel \| 配置”边栏选项卡上，单击“分析”。 

2. 在“Microsoft Sentinel \| 分析”边栏选项卡上，单击“规则模板”选项卡。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the types of rules you can create. Each rule is associated with a specific Data Source.

3. In the listing of rule templates, type <bpt id="p1">**</bpt>Suspicious<ept id="p1">**</ept> into the search bar form and click the <bpt id="p2">**</bpt>Suspicious number of resource creation or deployment<ept id="p2">**</ept> entry associated with the <bpt id="p3">**</bpt>Azure Activity<ept id="p3">**</ept> data source. And then, in the pane displaying the rule template properties, click <bpt id="p1">**</bpt>Create rule<ept id="p1">**</ept> (scroll to the right of the page if needed).

    >**注意**：此规则具有中等严重性。 

4. 在“分析规则向导 - 根据模板新建规则”边栏选项卡的“常规”选项卡上，接受默认设置，然后单击“下一步:设置规则逻辑 >”。

5. 在“分析规则向导 - 根据模板新建规则”边栏选项卡的“设置规则逻辑”选项卡上，接受默认设置，然后单击“下一步:事件设置(预览) >”。

6. 在“分析规则向导 - 根据模板新建规则”边栏选项卡的“事件设置”选项卡上，接受默认设置，然后单击“下一步:自动响应 >”。 

    >**注意**：在此处，你可以将作为逻辑应用实现的 Playbook 添加到规则中，以自动修复问题。

7. 在“分析规则向导 - 根据模板新建规则”边栏选项卡的“自动响应”选项卡上，接受默认设置，然后单击“下一步:查看 >”。 

8. 在“分析规则向导” - “根据模板新建规则”边栏选项卡的“查看并创建”选项卡上，单击“创建”。

    >**注意**：你现在有一个可用规则。

#### <a name="task-4-create-a-playbook"></a>任务 4：创建 Playbook

In this task, you will create a playbook. A security playbook is a collection of tasks that can be invoked by Microsoft Sentinel in response to an alert. 

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

    >对于本实验室中的所有资源，我们使用“美国东部”区域。

11. 在“逻辑应用设计器”边栏选项卡上，单击第一个连接步骤。

12. 单击“新增”，确保“租户”下拉列表中的条目包含 Azure AD 租户名称，然后单击“登录”。

13. 出现提示时，使用你在本实验室中使用 Azure 订阅具有的所有者或参与者角色的用户帐户进行登录。

14. 单击第二个连接步骤，在连接列表中选择第二个条目，该条目表示你在上一步中创建的连接。

15. 对于剩下的两个连接步骤，重复之前的步骤。

    >**注意**：确保任何步骤中都没有显示警告。

16. 在“逻辑应用设计器”边栏选项卡上，单击“保存”保存更改。

#### <a name="task-5-create-a-custom-alert-and-configure-a-playbook-as-an-automated-response"></a>任务 5：创建自定义警报并将 playbook 配置为自动响应

1. 在 Azure 门户中，导航回“Microsoft Sentinel \| 概述”边栏选项卡。

2. 在“Microsoft Sentinel \| 概述”边栏选项卡上的“配置”部分中，单击“分析”。

3. 在“Microsoft Sentinel \| 分析”边栏选项卡上，单击“+ 创建”，然后在下拉菜单中单击“计划的查询规则”。 

4. 在“分析规则向导” - “新建规则”边栏选项卡的“常规”选项卡上，请指定以下设置（将其他设置保留为默认值）：

    |设置|值|
    |---|---|
    |名称|playbook 演示|
    |策略|**初始访问**|

5. 单击“下一步:设置规则逻辑 >”。

6. 在“分析规则向导” - “新建规则”边栏选项卡的“设置规则逻辑”选项卡上，在“规则查询”文本框中，粘贴以下规则查询。 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**注意**：此规则标识删除实时 VM 访问策略。

    >请与讲师确认这是课堂上所使用的区域。 


7. 在“分析规则向导” - “新建规则”边栏选项卡的“设置规则逻辑”选项卡上，在“查询计划”部分，将“每运行一次查询”设置为“5 分钟”。

8. 在“分析规则向导 - 新建规则”边栏选项卡的“设置规则逻辑”选项卡上，接受其余设置的默认值，然后单击“下一步:事件设置 >”。

9. 在“分析规则向导 - 新建规则”边栏选项卡的“事件设置”选项卡上，接受默认设置，然后单击“下一步:自动响应 >”。 

10. 在“分析规则向导 - 新建规则”边栏选项卡的“自动响应”选项卡上，在“警报自动化(经典)”下拉列表中，选中“Change-Incident-Severity”条目旁边的复选框，然后单击“下一步:查看 >”。 

11. 在“分析规则向导 - 新建规则”边栏选项卡的“查看并创建”选项卡上，单击“创建”。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You now have a new active rule called <bpt id="p2">**</bpt>Playbook Demo<ept id="p2">**</ept>. If an event identified by the rue logic occurs, it will result in a medium severity alert, which will generate a corresponding incident.

#### <a name="task-6-invoke-an-incident-and-review-the-associated-actions"></a>任务 6：调用事件并查看关联操作。

1. 在 Azure 门户中，导航到“Microsoft Defender For Cloud \| 概述”边栏选项卡。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Check your secure score. By now it should have updated. 

2. 在“Microsoft Defender for Cloud \| 工作负载保护”边栏选项卡上，单击“高级保护”下的“实时 VM 访问”部分。

3. 在“Microsoft Defender for Cloud \| 实时 VM 访问”边栏选项卡上，在引用 myVM 虚拟机的行的右侧，单击“省略号”按钮，单击“删除”，然后单击“是”。

    ><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> If the VM is not listed in the <bpt id="p2">**</bpt>Just-in-time VMs<ept id="p2">**</ept>, navigate to <bpt id="p3">**</bpt>Virutal Machine<ept id="p3">**</ept> blade and click the <bpt id="p4">**</bpt>Configuration<ept id="p4">**</ept>, Click the <bpt id="p5">**</bpt>Enable the Just-in-time VMs<ept id="p5">**</ept> option under the <bpt id="p6">**</bpt>Just-in-time Vm's access<ept id="p6">**</ept>. Repeat the above step to navigate back to the <bpt id="p1">**</bpt>Microsoft Defender for Cloud<ept id="p1">**</ept> and refresh the page, the VM will appear.

4. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“活动日志”，然后按 Enter 键。

5. 导航到“活动日志”边栏选项卡，请注意“删除 JIT 网络访问策略”条目 。 

    >**注意**：这可能需要一分钟才能显示。 

6. 在 Azure 门户中，导航回“Microsoft Sentinel \| 概述”边栏选项卡。

7. 在“Microsoft Sentinel \| 概述”边栏选项卡上，查看仪表板，并验证它是否显示与删除实时 VM 访问策略相对应的警报。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It can take up to 5 minutes for alerts to appear on the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Overview<ept id="p2">**</ept> blade. If you are not seeing an alert at that point, run the query rule referenced in the previous task to verify that the Just In Time access policy deletion activity has been propagated to the Log Analytics workspace associated with your Microsoft Sentinel instance. If that is not the case, re-create the Just in time VM access policy and delete it again.

8. 在“Microsoft Sentinel \| 概述”边栏选项卡上的“威胁管理”部分中，单击“事件”。

9. 验证边栏选项卡是否显示严重性级别为中或高的事件。

    >**注意**：事件可能需要 5 分钟的时间才会在“Microsoft Sentinel \| 事件”边栏选项卡中显示。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Review the <bpt id="p2">**</bpt>Microsoft Sentinel <ph id="ph1">\|</ph> Playbooks<ept id="p2">**</ept> blade. You will find there the count of succesfull and failed runs.

    >**注意**：你可以选择为事件分配不同的严重性级别和状态。

> 结果：你已创建 Microsoft Sentinel 工作区，将其连接到 Azure 活动日志，已创建 playbook 和为响应删除实时 VM 访问策略而触发的自定义警报，并已验证配置是否有效。

**清理资源**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. 关闭 Cloud Shell 窗格。
