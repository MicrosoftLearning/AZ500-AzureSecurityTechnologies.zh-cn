---
lab:
  title: 02 - Azure Policy
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-02-azure-policy"></a>实验室 02：Azure Policy
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to create a proof of concept showing how Azure policy can be used. Specifically, you need to:

- 创建“允许的位置”策略，以确保仅在特定区域中创建资源。
- 测试以确保仅在允许的位置创建资源

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中将完成以下任务：

- 练习 1：实现 Azure Policy。 

## <a name="azure-policy-diagram"></a>Azure Policy 示意图

![image](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## <a name="instructions"></a>说明

### <a name="exercise-1-implement-azure-policy"></a>练习 1：实现 Azure Policy

#### <a name="estimated-timing-20-minutes"></a>预计用时：20 分钟

在本练习中，你将完成以下任务：

- 任务 1：创建 Azure 资源组。 
- 任务 2：创建“允许位置”策略分配。
- 任务 3：验证“允许的位置”策略分配是否正常工作。 

#### <a name="task-1-create-a-resource-group-for-the-lab"></a>任务 1：为本实验室创建资源组。 

在此任务中，你将为本实验室创建一个资源组。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以创建资源组（与讲师确认位置参数的值）：

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令列出资源组以验证是否创建了新资源组：

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. 关闭 Cloud Shell。

#### <a name="task-2-create-an-allowed-locations-policy-assignment"></a>任务 2：创建“允许位置”策略分配。

在此任务中，你将创建“允许位置”策略分配，并指定该策略可以使用哪个 Azure 区域。 

1. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“策略”，然后按“Enter”键。

1. 在“策略”边栏选项卡上的“创作”部分，选择“定义”  **** 。

1. Take a minute to browse the built-in definitions. Use the <bpt id="p1">**</bpt>Category<ept id="p1">**</ept> drop-down to filter the list of policies.

1. 在“搜索”文本框 中，键入“允许位置”。 

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The <bpt id="p2">**</bpt>Allowed locations<ept id="p2">**</ept> policy allows you to restrict location of resources, not resource groups. To restrict locations of resource groups, you can use the <bpt id="p1">**</bpt>Allowed locations for resource groups<ept id="p1">**</ept> policy.

1. 单击“允许位置”策略定义以显示其详细信息 ****  。 

   >你需要创建一个显示如何使用 Azure Policy 的概念证明。

1. 在“允许位置”边栏选项卡上，单击“分配”。

1. 在“允许位置”边栏选项卡的“基础”选项卡上，单击“范围”文本框旁边的省略号 (...) 按钮，然后在“范围”边栏选项卡上指定以下设置：

   |设置|值|
   |---|---|
   |订阅|Azure 订阅的名称|
   |资源组|AZ500LAB02|

1. 单击“选择”。

1. 在“允许位置”边栏选项卡上，在“基础”选项卡上指定以下设置（其他设置保留默认值）：

   |设置|值|
   |---|---|
   |分配名称|允许 AZ500LAB02 使用英国南部地区|
   |说明|对于 AZ500LAB02，仅允许在英国南部创建资源|
   |策略强制执行|**已启用**|

1. 单击“下一步” **** 。

1. 在“允许位置”边栏选项卡的“参数”选项卡上，在“允许位置”下拉列表中选择“英国南部”作为唯一的允许位置    。 

   >具体而言，需要： 

1. 单击“查看 + 创建”，再单击“创建”来创建策略分配 ****  ****  。 

   >**注意**：你会看到一条表示分配已成功的通知，此分配可能需要 30 分钟左右才能完成。

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The reason the Azure policy assignment might take up to 30 minutes to take effect is that is has to replicate globally. Typically this takes only a few minutes.  If the next task fails, simply wait a few minutes and attempt its steps again.

#### <a name="task-3-test-the-allowed-locations-policy-assignment"></a>任务 3：测试“允许位置”策略分配。

在此任务中，你将测试“允许位置”策略分配。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“虚拟网络”，然后按 Enter 键  。

1. 在“虚拟网络”边栏选项卡上，单击“+ 创建” **** 。

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: First, you will try to create a virtual network in East US. Since this is not an allowed location, the request should be blocked. 

1. 在“创建虚拟网络”边栏选项卡的“基本信息”选项卡上，指定以下设置（其他设置保留默认值）  ：

    |设置|值|
    |---|---|
    |资源组|AZ500LAB02|
    |名称|myVNet|
    |区域|**（美国）美国东部**|

1. 单击“查看 + 创建” **** 。 

1. 在“创建虚拟网络”边栏选项卡的“查看 + 创建”选项卡上，请注意“验证失败”消息  。 

    > **注意**：如果未出现“验证失败”警告，请单击“上一步”，然后再等待几分钟时间。

1. On the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab, click the error message link to open the <bpt id="p2">**</bpt>Policy Assignment<ept id="p2">**</ept> blade. You will see the policy assignment that restricts the location.

1. 关闭“策略分配”边栏选项卡，然后在“创建虚拟网络”边栏选项卡上，单击“基本信息”选项卡，然后在“区域”下拉列表中选择“(欧洲)英国南部”。

1. 单击“查看 + 创建”，确认验证是否通过，然后单击“创建”，确认是否成功创建虚拟网络 **** 。 

> 练习结果：在本练习中，你学习了如何通过选择内置策略定义并将其分配给资源组来应用 Azure Policy。

**清理资源**

> 对于本实验室中的所有资源，我们使用“美国东部”区域。

1. 请与讲师确认这是课堂上所使用的区域。

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```
1.  关闭 Cloud Shell 窗格。 
  
1. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“策略”，然后按“Enter”键。

1. 在“创作”部分，选择“分配”。

1. 在分配列表中，选择你在本实验室中创建的“允许位置”策略的名称。

1. 在策略分配上，选择“删除分配”，然后选择“是” 。
