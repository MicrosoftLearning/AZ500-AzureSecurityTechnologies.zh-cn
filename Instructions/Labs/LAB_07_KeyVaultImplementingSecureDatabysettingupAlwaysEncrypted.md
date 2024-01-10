---
lab:
  title: 07 - 密钥保管库（通过设置 Always Encrypted 来实现安全数据）
  module: Module 01 - Manage security operations
---

# 实验室 07：密钥保管库（通过设置 Always Encrypted 来实现安全数据）
# 学生实验室手册

## 实验室方案

你被要求创建一个概念证明应用程序，该应用程序将利用 Azure SQL 数据库对 Always Encrypted 功能的支持。 此方案中使用的所有机密和密钥应存储在密钥保管库中。 应在 Microsoft Entra ID 中注册该应用程序，以增强其安全状况。 为了实现这些目标，概念证明应包括：

- 创建 Azure 密钥保管库并在保管库中存储密钥和机密。
- 创建一个 SQL 数据库并使用 Always Encrypted 加密数据库表中的列内容。

>**注意**：对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

为了始终专注于与构建此概念验证相关的 Azure 的安全性，将从自动化 ARM 模板部署开始，使用 Visual Studio 2019 和 SQL Server Management Studio 19 设置虚拟机。

## 实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：通过 ARM 模板部署基础基础结构
- 练习 2：使用密钥和机密配置密钥保管库资源
- 练习 3：配置 Azure SQL 数据库和数据驱动的应用程序
- 练习 4：演示如何在加密 Azure SQL 数据库的过程中使用 Azure 密钥保管库

## 密钥保管库示意图

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/38c4ba6d-2fc7-45e5-b9a2-d5dbb4fbbcbc)

## Instructions

## 实验室文件：

- \\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json

- \\Allfiles\\Labs\\10\\program.cs

### 实验室总时间估算：60 分钟

### 练习 1：通过 ARM 模板部署基础基础结构

在本练习中，你将完成以下任务：

- 任务 1：部署 Azure VM 和 Azure SQL 数据库

#### 任务 1：部署 Azure VM 和 Azure SQL 数据库

在此任务中，你将部署 Azure VM，这将在部署过程中自动安装 Visual Studio 2019 和 SQL Server Management Studio 19。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“部署自定义模板”，然后按 Enter 键  。

3. 在“自定义部署”边栏选项卡中，单击“在编辑器中构建自己的模板”选项。

4. 在“编辑模板”边栏选项卡中，单击“加载文件”，找到 \\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json 文件并单击“打开”。

5. 在“编辑模板”边栏选项卡上，单击“保存”。

6. 在“自定义部署”边栏选项卡的“部署范围”下，请确保已配置以下设置（将其他设置保留为默认值）：

   |设置|值|
   |---|---|
   |订阅|将在此实验室中使用的 Azure 订阅的名称|
   |资源组|单击“新建”并键入名称“AZ500LAB10”|
   |位置|**美国东部**|
   |用户名|**学生**|
   |密码|请使用在实验室 02 > 练习 2 > 任务 1 > 步骤 3 中创建的个人密码****。|
   
   
    >**注意**：虽然可以更改用于登录虚拟机的管理凭据，但你不必这样做。

    >**注意**：若要确定可在其中预配 Azure VM 的 Azure 区域，请参阅 [https://azure.microsoft.com/en-us/regions/offers/](https://azure.microsoft.com/en-us/regions/offers/)

8. 单击“查看和创建”按钮，然后单击“创建”按钮确认部署。 

    >**注意**：这将启动本实验室所需的 Azure VM 和 Azure SQL 数据库的部署。 

    >**注意**：不要等待 ARM 模板部署完成，而是继续下一个练习。 该部署可能需要 20-25 分钟。 

### 练习 2：使用密钥和机密配置密钥保管库资源

>**注意**：对于本实验室中的所有资源，我们使用“美国东部”区域。 与你的教师确认这是你上课时使用的区域。 

在本练习中，你将完成以下任务：

- 任务 1：创建并配置密钥保管库。
- 任务 2：将密钥添加到密钥保管库
- 任务 3：向密钥保管库添加机密

#### 任务 1：创建并配置密钥保管库。

在此任务中，你将创建 Azure 密钥保管库资源， 还将配置 Azure 密钥保管库权限。

1. 单击 Azure 门户右上角的第一个图标（搜索栏旁边），打开 Cloud Shell。 如果出现提示，请选择“PowerShell”和“创建存储” 。

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令，以在资源组 AZ500LAB10 中创建 Azure 密钥保管库。 （如果在任务 1 中为此实验室的资源组选择了另一个名称，则也要对此任务使用该名称）。 密钥保管库名称必须是唯一的。 记住所选择的名称。 整个实验室都需要它。  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**注意**：最后一条命令的输出将显示保管库名称和保管库 URI。 保管库 URI 的格式为 `https://<vault_name>.vault.azure.net/`

4. 关闭 Cloud Shell 窗格。 

5. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“资源组”，然后按 Enter 键  。

6. 在“资源组”边栏选项卡上的资源组列表中，单击 AZ500LAB10（或先前为资源组选择的其他名称）条目。

7. 在“资源组”边栏选项卡上，单击表示新创建的密钥保管库的条目。 

8. 在“Key Vault”边栏选项卡上的“概述”部分，单击“访问策略”，然后单击“+ 创建”  。

9. 在“创建访问策略”边栏选项卡上，指定以下设置（所有其他设置保留默认值）： 

    |设置|值|
    |----|----|
    |从模板配置（可选）|密钥、机密和证书管理|
    |密钥权限|单击“全选”，总共会选中 9 个权限|
    |密钥权限/加密操作|单击“签名”，总共会选中 1 个权限|
    |机密权限|单击“全选”，总共会选中 7 个权限|
    |证书权限|单击“全选”，总共会选中 15 个权限|
    |选择主体|单击“未选择”，在“主体”边栏选项卡，选择你的用户帐户，然后单击“下一步”|
    |应用程序（可选）|单击“下一步”|
    |查看 + 创建|单击“创建”|
    
    >注意：前面的“查看 + 创建”操作将返回到“访问策略”页，其中列出了“应用程序”、“电子邮件”、“密钥权限”、“机密权限”和“证书权限”。
      
#### 任务 2：向密钥保管库中添加密钥

在此任务中，你将向密钥保管库添加密钥并查看有关该密钥的信息。 

1. 在 Azure 门户的 Cloud Shell 窗格中打开一个 PowerShell 会话。

2. 确保在“Cloud Shell”窗格左上方的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以将软件保护的密钥添加到密钥保管库： 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**注意**：密钥的名称为 MyLabKey

4. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以验证密钥是否已创建：

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

5. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以显示密钥标识符：

    ```powershell
    $key.key.kid
    ```

6. 最小化“Cloud Shell”窗格。 

7. 返回 Azure 门户，在“Key Vault”边栏选项卡上的“对象”部分，单击“密钥” 。

8. 在密钥列表中，单击“MyLabKey”条目，然后，在“MyLabKey”边栏选项卡上，单击代表密钥当前版本的条目。

    >**注意**：检查有关你创建的密钥的信息。

    >**注意**：你可以使用密钥标识符来引用任何密钥。 若要获取最新版本，请引用 `https://<key_vault_name>.vault.azure.net/keys/MyLabKey`，或通过 `https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>` 获取具体版本


#### 任务 3：向密钥保管库添加机密

1. 切换回 Cloud Shell 窗格。

2. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以创建具有安全字符串值的变量：

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

3.  在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令将机密添加到保管库：

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**注意**：机密名称为 SQLPassword。 

4.  在 Cloud Shell 窗格的 PowerShell 会话中，运行以下命令验证是否已创建机密。

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

5. 最小化“Cloud Shell”窗格。 

6. 在 Azure 门户中，导航回“Key Vault”边栏选项卡上的“对象”部分，单击“机密” 。

7. 在机密列表中，单击“SQLPassword”条目，然后在“SQLPassword”边栏选项卡上，单击代表机密当前版本的条目。

    >**注意**：检查创建的机密信息。

    >**注意**：若要获取最新机密版本，请引用 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>`，或通过引用 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>` 获取具体版本


### 练习 3：配置 Azure SQL 数据库和数据驱动的应用程序

在本练习中，你将完成以下任务：

- 任务 1：启用客户端应用程序访问 Azure SQL 数据库服务。
- 任务 2：创建一个允许应用程序访问密钥保管库的策略。
- 任务 3：检索 SQL Azure 数据库 ADO.NET 连接字符串 
- 任务 4：登录到运行 Visual Studio 2019 和 SQL Management Studio 19 的 Azure VM
- 任务 5：在 SQL 数据库中创建一个表，然后选择要加密的数据列


#### 任务 1：启用客户端应用程序访问 Azure SQL 数据库服务。 

在此任务中，可启用客户端应用程序访问 Azure SQL 数据库服务。 通过设置所需的身份验证并获取验证应用程序所需的应用程序 ID 和密钥来完成此操作。

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“应用注册” 然后按“Enter” 键。

2. 在“应用注册”选项边栏卡上，单击“+ 新注册”。 

3. 在“注册应用程序”边栏选项卡，指定以下设置（所有其他设置保留为默认值）：

    |设置|值|
    |----|----|
    |名称|sqlApp|
    |重定向 URI（可选）|Web 和 https://sqlapp|

4. 在“注册应用程序”边栏选项卡上，单击“注册”。 

    >**注意**：注册完成后，浏览器将自动将你重定向到“sqlApp”边栏选项卡。 

5. 在“sqlApp”边栏选项卡上，找到“应用程序(客户端) ID”的值。 

    >**注意**：记下此值。 稍后在下一个任务中将用到它。

6. 在“sqlApp”边栏选项卡的“管理”部分，单击“证书和机密”。

7. 在“sqlApp | 证书和机密”边栏选项卡的“客户端密码”部分，单击“+ 新客户端密码”

8. 在“添加客户端机密”窗格中，指定以下设置：

    |设置|值|
    |----|----|
    |说明|**Key1**|
    |Expires|**12 个月**|
    
9. 单击“添加”，更新应用程序的凭据。

10. 在“sqlApp | 证书和机密”边栏选项卡上，确定“Key1”的值。

    >**注意**：记下此值。 稍后在下一个任务中将用到它。 

    >**注意**：请确保在离开边栏选项卡之前复制该值。 完成后，将不再可能检索其明文值。


#### 任务 2：创建一个允许应用程序访问密钥保管库的策略。

在此任务中，将授予新注册的应用访问存储在密钥保管库中的机密的权限。

1. 在 Azure 门户的 Cloud Shell 窗格中打开一个 PowerShell 会话。

2. 确保在“Cloud Shell”窗格左上方的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令，创建用于存储上一个任务中记录的“应用程序(客户端) ID”的变量（用“应用程序(客户端) ID”的值替换 `<Azure_AD_Application_ID>` 占位符）：
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
4. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以创建存储密钥保管库名称的变量。
    ```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

5. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以将密钥保管库的权限授予在上一个任务中注册的应用程序：

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

6. 关闭 Cloud Shell 窗格。 


#### 任务 3：检索 SQL Azure 数据库 ADO.NET 连接字符串 

练习 1 中的 ARM 模板部署预配了一个 Azure SQL Server 实例和一个名为“医疗”的 Azure SQL 数据库。 你将使用新的表结构更新空数据库资源，并选择数据列进行加密

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“SQL 数据库”，然后按 Enter 键。

2. 在 SQL 数据库列表中，单击“medical(<randomsqlservername>)”条目。

    >**注意**：如果找不到数据库，则可能意味着在练习 1 中启动的部署尚未完成。 可以通过浏览到 Azure 资源组“AZ500LAB10”（或所选名称）并从“设置”窗格中选择“部署”来对此进行验证。  

3. 在 SQL 数据库边栏选项卡的“设置”部分，单击“连接字符串”。 

    >**注意**：该接口包括 ADO.NET、JDBC、ODBC、PHP 和 Go 的连接字符串。 
   
4. 记录“ADO.NET (SQL 身份验证)”连接字符串。 稍后需要用到此值。

    >**注意**：使用连接字符串时，请确保将 `{your_password}` 占位符替换为你在练习 1 中为部署配置的密码。

#### 任务 4：登录到运行 Visual Studio 2019 和 SQL Management Studio 19 的 Azure VM

在此任务中，登录到 Azure VM，这是在练习 1 中启动的部署。 此 Azure VM 托管 Visual Studio 2019 和 SQL Server Management Studio 19。

    >**Note**: Before you proceed with this task, ensure that the deployment you initiated in the first exercise has completed successfully. You can validate this by navigating to the blade of the Azure resource group "Az500Lab10" (or other name you chose) and selecting **Deployments** from the Settings pane.  

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键。

2. 在显示的虚拟机列表中，选择 az500-10-vm1 条目。 在 az500-10-vm1 边栏选项卡上的“基本”窗格上，记下“公共 IP 地址”。 稍后你将用到它。 

#### 任务 5：在 SQL 数据库中创建一个表，然后选择要加密的数据列

在此任务中，你将使用 SQL Server Management Studio 连接到 SQL 数据库并创建表， 随后使用 Azure 密钥保管库中自动生成的密钥对两个数据列进行加密。 

1. 在 Azure 门户中，导航到“医疗”SQL 数据库的边栏选项卡，在“概要”部分，标识服务器名称（复制到剪贴板），然后在工具栏中单击“设置服务器防火墙”。  

    >**注意**：记录服务器名称。 此任务稍后需要服务器名称。

2. 在“防火墙设置”边栏选项卡上，向下滚动到“规则名称”，单击“+ 添加防火墙规则”，然后指定以下设置 ： 

    |设置|值|
    |---|---|
    |规则名称|允许 Mgmt VM|
    |起始 IP|公共 IP 地址：az500-10-vm1|
    |结束 IP|公共 IP 地址：az500-10-vm1|

3. 单击“保存”保存更改并关闭确认窗格。 

    >**注意**：这将修改服务器防火墙设置，从而允许从在本实验室中部署的 Azure VM 的公共 IP 地址连接到医疗数据库。

4. 导航回“az500-10-vm1”边栏选项卡，单击“概述”，再单击“连接”，然后在下拉菜单中单击“RDP”。 

5. 请单击“下载 RDP 文件”，并通过远程桌面将其连接到 az500-10-vm1 Azure VM。 收到身份验证提示时，请提供以下凭据：

    |设置|值|
    |---|---|
    |用户名|**学生**|
    |密码|请使用在实验室 02 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。****|
    

    >**注意**：等待加载远程桌面会话和服务器管理器。 关闭服务器管理器。 

    >**注意**：本实验室中的其余步骤在 az500-10-vm1 Azure VM 的远程桌面会话中执行。 

7. 单击“**开始**”，在“**开始**”菜单中，展开“**Microsoft SQL Server Tools 19**”文件夹，然后单击“**Micosoft SQL Server Management Studio**”菜单项。

8. 在“连接到服务器”对话框中，指定以下设置： 

    |设置|值|
    |---|---|
    |服务器类型|**数据库引擎**|
    |服务器名称|此任务之前标识的服务器名称|
    |身份验证|**SQL Server 身份验证**|
    |用户名|**学生**|
    |密码|请使用在实验室 02 > 练习 2 > 任务 1 > 步骤 3 中创建的个人密码****。|


10. 在“连接到服务器”对话框中，单击“连接”。

11. 在“SQL Server Management Studio”控制台，在“对象浏览器”窗格，展开“数据库”文件夹。

12. 在“对象资源管理器”窗格，右键单击“医疗”数据库，然后单击“新查询”。

13. 将以下代码粘贴到查询窗口中，然后单击“执行” 。 这将创建一个患者表。

     ```sql
     CREATE TABLE [dbo].[Patients](
        [PatientId] [int] IDENTITY(1,1),
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NULL,
        [LastName] [nvarchar](50) NULL,
        [MiddleName] [nvarchar](50) NULL,
        [StreetAddress] [nvarchar](50) NULL,
        [City] [nvarchar](50) NULL,
        [ZipCode] [char](5) NULL,
        [State] [char](2) NULL,
        [BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
14. 成功创建表后，在“对象资源管理器”窗格中，展开“医疗”数据库节点、“表”节点，右键单击“dbo.Patients”节点，然后单击“加密列”。 

    >**注意**：这将启动 Always Encrypted 向导。

15. 在“简介”  页上，单击“下一步” 。

16. 在“列选择”页面，选择“SSN”和“Birthdate”列，将“SSN”列的加密类型设置为“确定”，将“Birthdate”列的加密类型设置为“随机”，然后单击“下一步”。

    >**注意**：执行加密时，如果引发了与 Rotary(Microsoft.SQLServer.Management.ServiceManagement) 相关的类似“调用目标引发了异常”的任何错误，请确保“轮换策略操作”的“密钥权限”值处于未选中状态，如果不在 Azure 门户中，请导航到“密钥保管库” >> “访问策略” >> “密钥权限”>> 取消选中“轮换策略操作”下的所有值 >> 在“特权密钥操作”下 >> 取消选中“发布”           。

17. 在“主密钥配置”页面上，选择“Azure 密钥保管库”，单击“登录”，当系统出现提示时，使用本实验室前面部分中用于预配 Azure 密钥保管库实例的用户帐户进行验证，确保该密钥保管库显示在“选择一个 Azure 密钥保管库”下拉列表中，然后单击“下一步”。

18. 在“运行设置”页面，单击“下一步”。
    
19. 在“摘要”页面上，单击“完成”以继续进行加密。 出现提示时，使用你之前在本实验室中用于预配 Azure 密钥保管库实例的相同用户帐户再次登录。

20. 加密过程完成后，在“结果”页面上，单击“关闭”。

21. 在“SQL Server Management Studio”控制台，在“对象资源管理器”窗格中的“医疗”节点下，展开“安全”和“始终加密的密钥”子节点。 

    >**注意**：“Always Encrypted 密钥”子节点包含“Column Master Keys”和“Column Encryption Keys”子文件夹。


### 练习 4：演示如何在加密 Azure SQL 数据库的过程中使用 Azure 密钥保管库

在本练习中，你将完成以下任务：

- 任务 1：运行数据驱动的应用程序，以演示在加密 Azure SQL 数据库的过程中如何使用 Azure 密钥保管库

#### 任务 1：运行数据驱动的应用程序，以演示在加密 Azure SQL 数据库的过程中如何使用 Azure 密钥保管库

使用 Visual Studio 创建一个控制台应用程序，以将数据加载到加密列中，然后使用访问密钥保管库中的密钥的连接字符串安全地访问该数据。

1. 从 az500-10-vm1 的 RDP 会话，从“开始菜单”启动 Visual Studio 2019。

2. 切换到显示 Visual Studio 2019 欢迎消息的窗口，单击“登录”按钮，然后在系统出现提示时提供用于对本实验室中使用的 Azure 订阅进行验证的凭据。

3. 在“开始”页面上，单击“新建项目”。 

4. 在项目模板列表中，搜索“控制台应用 (.NET Framework)”，在结果列表中，单击“C#”对应的“控制台应用 (.NET Framework)”，然后单击“下一步”。

5. 在“配置新项目”页面上，指定以下设置（将其他设置保留为默认值），然后单击“创建”：

    |设置|值|
    |---|---|
    |项目名称|OpsEncrypt|
    |解决方案名称|OpsEncrypt|
    |框架|**.NET Framework 4.7.2**|

6. 在 Visual Studio 控制台中，单击“工具”菜单，在下拉菜单中，单击“NuGet 包管理器”，然后在级联菜单中单击“包管理器控制台”。

7. 在“包管理器控制台”窗格中运行以下命令以安装需要使用的第一个 NuGet 包 ：

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

8. 在“包管理器控制台”窗格中运行以下命令，安装需要使用的第二个 NuGet 包 ：

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
9. 将 RDP 会话最小化到 Azure 虚拟机，然后导航到 \\Allfiles\\Labs\\10\\program.cs，在记事本中将其打开，然后将其内容复制到剪贴板中。

10. 返回到 RDP 会话，然后在 Visual Studio 控制台的“解决方案资源管理器”窗口，单击“Program.cs”并将其内容替换为复制到剪贴板中的代码 。

11. 在 Visual Studio 窗口的“Program.cs”窗格中的第 15 行，用你之前在本实验室中记录的 Azure SQL 数据库“ADO.NET”连接字符串替换 `<connection string noted earlier>` 占位符。 在连接字符串中，将 `{your_password}` 占位符替换为在练习 1 的部署中指定的密码。 如果将字符串保存在实验室计算机上，可能需要离开 RDP 会话来复制 ADO 字符串，然后返回到 Azure 虚拟机将其粘贴到其中。

12. 在 Visual Studio 窗口的“Program.cs”窗格中的第 16 行，用你之前在实验室中记录的注册应用的“应用程序(客户端) ID”值替换 `<client id noted earlier>` 占位符。 

13. 在 Visual Studio 窗口的“Program.cs”窗格中的第 17 行，用你之前在实验室中记录的注册应用的“Key1”值替换 `<key value noted earlier>` 占位符。 

14. 在 Visual Studio 控制台中，单击“开始”按钮以启动控制台应用程序的构建并开启它。

15. 该应用程序将启动“命令提示符”窗口。 当系统提示输入密码时，请键入在练习 1 的部署中指定的密码以连接到 Azure SQL 数据库。 

16. 让控制台应用保持运行，然后切换到“SQL Management Studio”控制台。 

17. 在“对象资源管理器”窗格中，右键单击“医学数据库”，然后在右键菜单中单击“新建查询”。

18. 在查询窗口中运行以下查询，验证从控制台应用加载到数据库中的数据是否已加密。

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

19. 返回到控制台应用程序，此处会提示你输入有效的 SSN。 这将查询数据的加密列。 在“命令提示符”处，键入以下内容，然后按 Enter 键：

    ```cmd
    999-99-0003
    ```

    >**注意**：验证查询返回的数据未加密。

20. 要终止控制台应用，请按 Enter 键

**清理资源**

> 记得删除所有不再使用的新建 Azure 资源。 删除未使用的资源，确保不产生意外成本。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 

2. 在 Cloud Shell 窗格左上方的下拉菜单中，根据需要选择“PowerShell”，并在出现提示时单击“确认” 。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以删除你在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

4.  关闭 Cloud Shell 窗格。 
