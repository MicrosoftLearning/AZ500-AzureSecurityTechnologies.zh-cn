---
lab:
  title: 11 - 保护 Azure SQL 数据库
  module: Module 03 - Secure data and applications
---

# <a name="lab-11-securing-azure-sql-database"></a>实验室 11：保护 Azure SQL 数据库
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to review security features for Azure SQL database. Specifically, you are interested in:

- 防范诸如 SQL 注入和数据外泄之类的攻击。 
- 能够发现数据库信息并将其分类为机密等类别。 
- 能够审核数据库服务器和数据库查询以及日志事件。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：实现 SQL 数据库安全功能

## <a name="securing-azure-sql-database-diagram"></a>保护 Azure SQL 数据库的示意图

![image](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## <a name="instructions"></a>Instructions

## <a name="lab-files"></a>实验室文件：

- \\Allfiles\\Labs\\11\\azuredeploy.json

### <a name="exercise-1-implement-sql-database-security-features"></a>练习 1：实现 SQL 数据库安全功能

### <a name="estimated-timing-30-minutes"></a>预计用时：30 分钟

在本练习中，你将完成以下任务：

- 任务 1：部署 Azure SQL 数据库
- 任务 2：配置高级数据保护
- 任务 3：配置数据分类
- 任务 4：配置审核

#### <a name="task-1-deploy-an-azure-sql-database"></a>任务 1：部署 Azure SQL 数据库

在此任务中，你将使用模板来部署实验室基础结构。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“部署自定义模板”，然后按 Enter 键  。

3. 在“自定义部署”边栏选项卡中，单击“在编辑器中构建自己的模板”选项。

4. 在“编辑模板”边栏选项卡中，单击“加载文件”，找到 \\Allfiles\\Labs\\11\\azuredeploy.json 文件并单击“打开”。

    >**注意**：查看模板内容，并注意该模板可部署 Azure SQL 数据库。

5. 在“编辑模板”边栏选项卡上，单击“保存”。

6. 在“自定义部署”边栏选项卡上，确保已配置以下设置（其他设置保留默认值）：

   |设置|值|
   |---|---|
   |订阅|将在此实验室中使用的 Azure 订阅的名称|
   |资源组|单击“新建”并键入名称“AZ500LAB11”|
   |位置|**（美国）美国东部**|

7. 单击“查看 + 创建”，然后单击“创建”。

    >备注：请等待部署完成。 

#### <a name="task-2-configure-advanced-data-protection"></a>任务 2：配置高级数据保护

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“资源组”，然后按 Enter 键  。

2. 在“资源组”边栏选项卡中，在资源组列表中，单击“AZ500LAB11”条目。

3. 在“AZ500LAB11”边栏选项卡中，单击表示新创建的 SQL Server 的条目。

4. On the SQL server blade, in the <bpt id="p1">**</bpt>Security<ept id="p1">**</ept> section, click <bpt id="p2">**</bpt>Microsoft Defender for Cloud<ept id="p2">**</ept>. Scroll to the bottom of the <bpt id="p1">**</bpt>Getting started<ept id="p1">**</ept> page and select <bpt id="p2">**</bpt>Upgrade<ept id="p2">**</ept>. On the <bpt id="p1">**</bpt>Install agents<ept id="p1">**</ept> tab select <bpt id="p2">**</bpt>Install agents<ept id="p2">**</ept>.

5. 在“SQL Server”边栏选项卡的“安全性”部分中，单击“Microsoft Defender for Cloud”，选择“启用 Microsoft Defender for SQL”。

      >**注意**：请等待通知指示 Azure Defender SQL 已成功启用。

6. 在“SQL Server”边栏选项卡的“安全性”部分中，在“Microsoft Defender for Cloud”页上的“Microsoft Defender for SQL:在服务器级别启用(配置)”参数中，单击“(配置)”。   

      >**注意**：如果未显示“(配置)”，请刷新浏览器。
    
7. 在“服务器设置”边栏选项卡上，查看有关定价和试用期、漏洞评估设置和高级威胁防护设置的信息 。

8. 返回到“Microsoft Defender for Cloud”边栏选项卡，查看“建议”和“安全警报”。

      ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It may take 10-15 minutes for recommendations to appear on the <bpt id="p2">**</bpt>Microsoft Defender for Cloud<ept id="p2">**</ept> blade. Rather than waiting, proceed to the next task but consider returning to this blade once you complete all the remaining tasks.


#### <a name="task-3-configure-data-classification"></a>任务 3：配置数据分类

在本任务中，你将发现 SQL 数据库中的信息并对其进行分类，以实现 GPDR 和数据保护合规性。

1. 在“SQL Server”边栏选项卡上的“设置”部分，单击“SQL 数据库”。

2. 在数据库列表中，选择 AZ500LabDb 条目。 

3. 在 AZ500LabDb SQL 数据库边栏选项卡的“安全”部分，单击“数据发现和分类”。

4. 在“数据发现与分类”边栏选项卡中，单击“分类”选项卡。

    >**注意**：分类引擎会扫描数据库以查找包含潜在敏感数据的列，并提供推荐列分类列表。

5. 单击边栏选项卡顶部蓝色栏中显示的文本消息“我们找到了 15 个带有分类建议的列”。

6. 查看列出的列和推荐的敏感度标签。 

7. 启用“全选”复选框，然后单击“接受所选的建议”。

    >**注意**：或者，可以只选择某些列，而忽略其他列。 

    >**注意**：你可以选择更改信息类型和敏感度标签。 

8. 完成审核后，单击“保存”。 

    >**注意**：这将完成分类，并使用新的分类元数据持久地标记数据库列。 

9. 返回到“数据发现和分类”边栏选项卡中的“概述”选项卡，注意它已更新，用于说明最新的分类信息。 

#### <a name="task-4--configure-auditing"></a>任务 4：配置审核 

在此任务中，你将首先配置服务器级审核，然后配置数据库级审核。 

1. 在 Azure 门户中，导航回“SQL Server”边栏选项卡。

2. 在“SQL Server”边栏选项卡上的“安全”部分，单击“审核”。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is server level auditing. The default auditing settings include all the queries and stored procedures executed against the database, as well as successful and failed logins.

3. 将“启用 Azure SQL 审核”开关设置为“开”以启用审核。 

4. 选择“存储”复选框，将显示“订阅”和“存储帐户”的输入框。

5. 从下拉列表中选择“订阅”。

6. 单击“存储帐户”并选择“新建”。

7. 在“创建存储帐户”边栏选项卡上的“名称”框中，键入一个由 3 至 24 个小写字母和数字组成的全局唯一名称，然后单击“确定”， 

    >**注意**：可能需要刷新浏览器，存储帐户才会变得可用。

8. 返回到“审核”边栏选项卡，在“高级属性”下，将“保留(天数)”设置为“5”。 

9. 在“审核”边栏选项卡上，单击“保存”以保存审核设置。 

    >你需要查看 Azure SQL 数据库的安全功能。

10. 在“服务器”边栏选项卡上的“设置”部分，单击“SQL 数据库”。

11. 在数据库列表中，选择 AZ500LabDb 条目。 

12. 在 AZ500LabDb SQL 数据库边栏选项卡上的“安全”部分，单击“审核”。 

    >具体来说，你感兴趣的是： 
  
    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Audits can be written to an Azure storage account, to a Log Analytics workspace, or to the Event Hub. You can configure any combination of these options.

    >**注意**：如果在服务器上启用基于存储的审核，则无论数据库设置如何，该审核将始终应用于数据库。

13. 单击“查看审核日志”。

14. 在“审核记录”边栏选项卡中，请注意，你可以在服务器审核和数据库审核之间切换。 

    >**注意**：由于此 SQL Server 和数据库是最近创建的，因此目前不太可能有任何事件可用。 

> 结果：你已创建 SQL Server 和数据库，已配置数据分类和审核。 


**清理资源**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. 关闭 Cloud Shell 窗格。 
