---
lab:
  title: 06 - 实现目录同步
  module: Module 01 - Manage Identity and Access
---

# 实验室 6：实现目录同步
# 学生实验室手册

## 实验室方案

你需要创建概念证明，以演示如何将本地 Microsoft Entra 域服务环境与 Microsoft Entra 租户集成。 具体来说，你需要：

- 通过部署托管 Microsoft Entra 域服务域控制器的 Azure VM 实现单域 Microsoft Entra 域服务林
- 创建并配置 Microsoft Entra 租户
- 将 Microsoft Entra 域服务林与 Microsoft Entra 租户同步

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：部署托管 Microsoft Entra ID 域控制器的 Azure VM
- 练习 2：创建并配置 Microsoft Entra 租户
- 练习 3：将 Microsoft Entra ID 林与 Microsoft Entra 租户同步

## 实现目录同步

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## 说明

### 练习 1：部署托管 Microsoft Entra ID 域控制器的 Azure VM

### 预计用时：10 分钟

在本练习中，你将完成以下任务：

- 任务 1：识别 Azure VM 部署的可用 DNS 名称
- 任务 2：使用 ARM 模板部署托管 Microsoft Entra ID 域控制器的 Azure VM

#### 任务 1：识别 Azure VM 部署的可用 DNS 名称

在此任务中，你将为 Azure VM 部署标识 DNS 名称。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。 如果出现提示，请单击“PowerShell”和“创建存储”。

3. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

4. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令标识可用于本练习下一个任务中的 Azure VM 部署的可用 DNS 名称：

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**注意**：将 `<custom-label>` 占位符替换为可能是全局唯一的有效 DNS 名称。 将 `<location>` 占位符替换为要将 Azure VM 部署到其中的区域的名称，该 Azure VM 将托管在本实验室中使用的 Active Directory 域控制器。

    >**注意**：若要确定可在其中预配 Azure VM 的 Azure 区域，请参阅 [https://azure.microsoft.com/en-us/regions/offers/](https://azure.microsoft.com/en-us/regions/offers/)

5. 验证命令是否返回 True。 如果不是，使用不同的 `<custom-label>` 值重新运行相同的命令，直到命令返回 True。

6. 记录返回正确结果的 `<custom-label>` 的值。 稍后在下一个任务中将用到它。

7. 关闭 Cloud Shell。

#### 任务 2：使用 ARM 模板部署托管 Microsoft Entra ID 域控制器的 Azure VM

在此任务中，你将部署托管 Microsoft Entra ID 域控制器的 Azure VM

1. 在同一浏览器窗口中，打开另一个浏览器标签页，并导航到 https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain。

2. 在“使用新 AD 林创建 Azure VM”页上，单击“部署到 Azure” 。 这会自动将浏览器重定向到 Azure 门户中的“使用新的 AD 林创建 Azure VM”边栏选项卡。 

3. 在“使用新的 AD 林创建 Azure VM”边栏选项卡中，单击“编辑参数”。

4. 在“编辑参数”边栏选项卡中，单击“加载文件”，在“打开”对话框中，导航到文件夹 \\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json，单击“打开”，然后单击“保存”。

5. 在“使用新的 AD 林创建 Azure VM”边栏选项卡中，指定以下设置（将其他设置保留为默认值）：

   |设置|值|
   |---|---|
   |订阅|Azure 订阅的名称|
   |资源组|单击“新建”并键入名称“AZ500LAB06”|
   |区域|你在上一个任务中标识的 Azure 区域|
   |管理员用户名|**学生**|
   |管理员密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
   |域名|adatum.com|
   |DNS 前缀|在上一个任务中标识的 DNS 主机名|
   |VM 大小|**Standard_D2s_v3**|

6. 在“使用新的 AD 林创建 Azure VM”边栏选项卡中，单击“查看 + 创建”，然后单击“创建”。

    >**注意**：请勿等待部署完成，而是继续进行下一个练习。 部署可能需要大约 15 分钟。 在本实验室的第三个练习中，你将使用此任务中部署的虚拟机。

> 结果：完成此练习后，你已经借助 Azure 资源管理器模板启动了一个 Azure VM 的部署，该 Azure VM 将用来托管 Microsoft Entra ID 域控制器


### 练习 2：创建并配置 Microsoft Entra 租户 

### 预计用时：20 分钟

在本练习中，你将完成以下任务：

- 任务 1：创建 Microsoft Entra 租户
- 任务 2：向新的 Microsoft Entra 租户中添加自定义 DNS 名称
- 任务 3：创建具有全局管理员角色的 Microsoft Entra ID 用户

#### 任务 1：创建 Microsoft Entra 租户

在此任务中，你将创建一个要在此实验室中使用的新 Microsoft Entra ID 租户。 

1. 在 Azure 门户中，在 Azure 门户页顶部的“搜索资源、服务和文档”**** 文本框中，键入“Microsoft Entra ID”****，然后按 Enter**** 键。

2. 在显示当前 Microsoft Entra 租户的“概述”的边栏选项卡上，单击“管理租户”，然后在下一个屏幕上单击“+ 创建”。************

3. 在“创建租户”**** 边栏选项卡的“基本信息”**** 选项卡上，确保选中“Microsoft Entra ID”**** 选项，然后选择“下一步: 配置”****。

4. 在“创建目录”边栏选项卡的“配置”选项卡上，指定以下设置：

   |设置|值|
   |---|---|
   |组织名称|AdatumSync|
   |初始域名|由字母和数字组合组成的唯一名称|
   |国家或地区|**美国**|

    >**注意**：记录初始域名。 本实验室中稍后会用到它。

    >**注意**：“初始域名”文本框中的绿色复选标记将指示你键入的域名是否有效且唯一。 （记录初始域名以供以后使用）。

5. 依次单击“查看 + 创建”、“创建”。

    >**注意**：等待新租户完成创建。 使用“通知”图标监视部署状态。 

#### 任务 2：将自定义 DNS 名称添加到新的 Azure AD 租户

在此任务中，你将自定义 DNS 名称添加到新的 Azure AD 租户。 

1. 在 Azure 门户的工具栏中，单击位于 Cloud Shell 图标的右侧的“目录 + 订阅”图标。 

2. 在“目录 + 订阅”边栏选项卡中，选择新建的租户“AdatumSync”一行，然后单击“切换”按钮。

    >**注意**：如果 AdatumSync 条目未出现在“目录 +订阅”筛选器列表中，则可能需刷新浏览器窗口。

3. 在“AdatumSync”\|Microsoft Entra ID 边栏选项卡的“管理”部分中单击“自定义域名”。************

4. 在“AdatumSync \| 自定义域名”边栏选项卡中，单击“+ 添加自定义域”。

5. 在“自定义域名”边栏选项卡的“自定义域名”文本框中，键入“adatum.com”，然后单击“添加域”。

6. 在“adatum.com”边栏选项卡中，查看执行 Microsoft Entra 域名验证所需信息，然后选择“删除”两次。******** 

    >**注意**：由于你没有 adatum.com DNS 域名，因此无法完成验证过程。 这不会阻止将 Microsoft Entra 域服务域 adatum.com 与 Microsoft Entra 租户同步****。 因此要使用 Microsoft Entra 租户的初始 DNS 名称（以 onmicrosoft.com**** 后缀结尾），该名称在上一个任务中已标识。 但请记住，这会造成 Microsoft Entra 域服务域的 DNS 域名和 Microsoft Entra 租户的 DNS 名称不同。 也就是，Adatum 用户在登录到 Microsoft Entra 域服务域和登录到 Microsoft Entra 租户时需要使用不同的名称。

#### 任务 3：创建具有全局管理员角色的 Microsoft Entra ID 用户

在此任务中，你将添加新的 Microsoft Entra ID 用户并为其分配全局管理员角色。 

1. 在“AdatumSync”Microsoft Entra 租户边栏选项卡的“管理”部分中，单击“用户”。************

2. 在“用户 | 所有用户”边栏选项卡上，依次单击“+ 新建用户”和“创建新用户”  。

3. 在“新建用户”边栏选项卡上，确保选中“创建用户”选项，并指定“基本信息”选项卡上的以下设置（保留所有其他设置的默认值），然后单击“下一步: 属性 >”：

   |设置|值|
   |---|---|
   |用户名|syncadmin|
   |名称|syncadmin|
   |密码|确保选中“自动生成密码”选项，然后单击“显示密码”|

    >**注意**：记录完整的用户名。 你可以通过单击显示域名的下拉列表右侧的“复制到剪贴板”按钮来复制其值，然后将值粘贴到记事本文档中。 稍后需要在本实验室用到它。

    >注意：通过单击“密码”文本框右侧的“复制到剪贴板”按钮然后粘贴到记事本文档中来记录用户的密码。 稍后需要在本实验室用到它。

4. 在“属性”选项卡上，滚动到底部并指定“使用位置: 美国”（保留所有其他设置的默认值），然后单击“下一页: 分配 >”。

5. 在“分配”选项卡上，单击“+ 添加角色”，搜索并选择“全局管理员”，然后单击“选择”。 依次单击“查看 + 创建”、“创建”。
   
    >**** 备注：要实现 Microsoft Entra Connect，需要具有全局管理员角色的 Azure AD 用户。

6. 打开 InPrivate 浏览器窗口。

7. 导航到 Azure 门户 (`https://portal.azure.com/`)，并使用 syncadmin 用户帐户登录。 出现提示时，请将之前在此任务中记录的密码更改为符合复杂性要求的密码，并记录该密码以供将来参考。 在以后的任务中，系统会提示你输入此密码。

    >**** 备注：要登录，需要提供 syncadmin 用户帐户的完全限定名称，包括之前在此任务中记录的 Microsoft Entra 租户 DNS 域名。**** 该用户名的格式为 syncadmin@`<your_tenant_name>`.onmicrosoft.com，其中 `<your_tenant_name>` 占位符代表你的唯一 Microsoft Entra 租户名称。 

8. 以 syncadmin 用户名注销并关闭 InPrivate 浏览器窗口。

> 结果****：完成此练习后，你已经创建了 Microsoft Entra 租户，知道了如何为新的 Microsoft Entra 租户添加自定义 DNS 名称，并创建了具有全局管理员角色的 Azure AD 用户。


### 练习 3：将 Microsoft Entra ID 林与 Microsoft Entra 租户同步

### 预计用时：20 分钟

在本练习中，你将完成以下任务：

- 任务 1：为目录同步准备 Microsoft Entra 域服务
- 任务 2：安装 Microsoft Entra Connect
- 任务 3：验证目录同步

#### 任务 1：为目录同步准备 Microsoft Entra 域服务

在此任务中，你将连接到运行 Microsoft Entra 域服务域控制器的 Azure VM 并创建目录同步帐户。 

   > 开始本任务之前，请确保在本实验室第一个练习中开始的模板部署已经完成。

1. 在 Azure 门户中，将“目录 + 订阅”筛选器设置为与在本实验室第一个练习中部署了 Azure VM 的 Azure 订阅相关联的 Microsoft Entra 租户。****

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键  。

3. 在“虚拟机”边栏选项卡中，单击“adVM”条目。 

4. 在“adVM”边栏选项卡中，单击“连接”，然后在下拉菜单中单击“RDP”。 

5. 在“IP 地址”下拉列表中，选择“负载均衡器公共 IP 地址”，然后单击“下载 RDP 文件”并使用它通过远程桌面连接到 adVM Azure VM。 收到身份验证提示时，请提供以下凭据：

   |设置|值|
   |---|---|
   |用户名|**学生**|
   |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >**注意**：等待加载远程桌面会话和服务器管理器。  

    >**注意**：在远程桌面会话中，对 adVM Azure VM 执行以下步骤。

    >注意：如果“负载均衡器公共 IP 地址”在 RDP 边栏选项卡的“IP 地址”下拉列表中不可用，请在 Azure 门户中搜索“公共 IP 地址”，选择“adPublicIP”并记下其 IP 地址。 单击“开始”按钮，键入“MSTSC”，然后按 Enter 启动远程桌面客户端。 在“计算机:”文本框中键入负载均衡器的公共 IP 地址，然后单击“连接”。

6. 在“服务器管理器”中单击“工具”，然后在下拉菜单中单击“Microsoft Entra ID 管理中心”。************

7. 在“Microsoft Entra ID 管理中心”中，单击“adatum (本地)”，在“任务”窗格中的域名“adatum (本地)”下单击“新建”，然后在级联菜单中单击“组织单位”。************************

8. 在“创建组织单位”窗口，在“名称”文本框中，键入“ToSync”，然后单击“确定”。

9. 双击新建的 ToSync 组织单位，使其内容出现在 Microsoft Entra ID 管理中心控制台的“详细信息”窗格中。**** 

10. 在“任务”窗格中，在“ToSync”部分，单击“新建”，然后在级联菜单中单击“用户”。

11. 在“创建用户”窗口，使用以下设置新建用户帐户（将其他用户保留其现有值），然后单击“确定”：
    
    |设置|值|
    |---|---|
    |全名|aduser1|
    |用户 UPN 登录|aduser1|
    |用户 SamAccountName 登录|aduser1|
    |“密码”和“确认密码”|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
    |其他密码选项|**密码永不过期**|


#### 任务 2：安装 Microsoft Entra Connect

在此任务中，你将在虚拟机上安装 Microsoft Entra Connect。 

1. 在与 adVM 的远程桌面会话中，使用 Microsoft Edge 导航到 Azure 门户 (https://portal.azure.com)，然后使用你在上一个练习中创建的 syncadmin 用户帐户登录。 出现提示时，请指定在上一练习中记录的完整用户主体名称和密码。

2. 在 Azure 门户中，在 Azure 门户页顶部的“搜索资源、服务和文档”**** 文本框中，键入“Microsoft Entra ID”****，然后按 Enter**** 键。

3. 在 Azure 门户中的“AdatumSync \| 概述”边栏选项卡上，在左侧导航面板中的“管理”下，单击“Microsoft Entra Connect”。************

4. 在“Microsoft Entra Connect \| 开始”边栏选项卡上，单击左侧导航面板中的“Connect Sync”，然后单击“下载 Microsoft Entra Connect”链接。************ 你将被重定向到 Microsoft Entra Connect 下载页。****

5. 在 Microsoft Entra Connect 下载页中单击“下载”。********

6. 出现提示时，单击“运行”以启动 Microsoft Entra Connect 向导。********

7. 在 Microsoft Entra Connect 向导的“欢迎使用 Microsoft Entra Connect”页面中，单击复选框“我同意许可条款和隐私声明”，然后单击“继续”。****************

8. 在 Microsoft Entra Connect 向导的“快速设置”页中，单击“自定义”选项。************

9. 在“安装所需组件”页面上，取消选择所有可选配置选项，然后单击“安装”。

10. 在“用户登录”页面上，确保仅启用“密码哈希同步”，然后单击“下一步”。

11. 在“连接到 Microsoft Entra ID”页上，使用在上一个练习中创建的 syncadmin 用户帐户凭据完成身份验证，然后单击“下一步”。************ 

12. 在“连接目录”页面上，单击“adatum.com”林条目右侧的“添加目录”按钮。

13. 在“AD 林帐户”窗口中，确保选中“创建新的 Microsoft Entra ID 帐户”选项，指定以下凭据，然后单击“确定”：************

    |设置|值|
    |---|---|
    |用户名|ADATUM\\Student|
    |密码|请使用在实验室 06 > 练习 1 > 任务 2 中创建的个人密码|

14. 返回到“连接目录”页面，确保 adatum.com 条目显示为已配置的目录，然后单击“下一步”

15. 在“Microsoft Entra ID 登录配置”页中，请注意警告“如果 UPN 后缀与已验证的域名不一致，用户将无法使用本地凭据登录 Microsoft Entra ID”****，启用复选框“继续操作且不将所有 UPN 后缀与已验证的域进行匹配”****，然后单击“下一步”。********

    >**** 备注：如前所述，这在预料之中，因为无法验证自定义的 Microsoft Entra ID DNS 域 adatum.com。****

16. 在“域和 OU 筛选”页上，单击“同步所选域和 OU”选项，并清除域名 adatum.com 旁边的复选框。 单击展开 adatum.com，仅选中“ToSync”OU 旁边的复选框，然后单击“下一步”。

17. 在“唯一标识用户”页面上，接受默认设置，然后单击“下一步”。

18. 在“筛选用户和设备”页面上，接受默认设置，然后单击“下一步”。

19. 在“可选功能”页面上，接受默认设置，然后单击“下一步”。

20. 在“已准备好进行配置”页面上，确保“配置完成后启动同步过程”复选框已选择，然后单击“安装”。

    >**注意**：安装需要约 2 分钟。

21. 查看“配置完成”页上的信息，然后单击“退出”以关闭 Microsoft Entra Connect 窗口。************


#### 任务 3：验证目录同步

在此任务中，你将验证目录同步工作是否正常。 

1. 在与 adVM 的远程桌面会话中，在显示 Azure 门户的 Microsoft Edge 窗口中，导航到 Microsoft Entra ID 租户 Adatum Lab 的“用户 - 所有用户(预览)”边栏选项卡。********

2. 在“用户 \| 所有用户”边栏选项卡上，注意用户对象列表包括 aduser1 帐户。 

   >**注意**：你可能需要等待几分钟，然后选择“刷新”以显示 aduser1 用户帐户。

3. 单击 aduser1 帐户，然后选择“属性”选项卡。向下滚动到“本地”部分，请注意，“启用本地同步”属性已设置为“是”。

4. 在 aduser1 边栏选项卡中，在“职位信息”部分中，请注意，“部门”属性未设置。

5. 在与 adVM 的远程桌面会话中，切换到 Microsoft Entra ID 管理中心，在“ToSync”OU 的对象列表中选择“aduser1”条目，然后在“任务”窗格的“aduser1”部分中选择“属性”。****************************

6. 在 aduser1 窗口的“组织”部分，在“部门”文本框中键入“销售”，然后选择“确定”。

7. 在与 adVM 的远程桌面会话中，启动“Windows PowerShell”。

8. 在“管理员: Windows PowerShell”控制台中运行以下命令，以启动 Microsoft Entra Connect 增量同步：****

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. 切换到显示“aduser1”边栏选项卡的 Microsoft Edge 窗口，刷新页面，注意“部门”属性已设置为“销售”。

    >注意：可能需要等待最多三分钟，如果“部门”属性仍未设置，请再次刷新页面。

> **** 结果：完成本练习后，你已准备好用于进行目录同步的 Microsoft Entra 域服务，安装了 Microsoft Entra Connect，并且验证了目录同步。


**清理资源**

>**** 备注：首先禁用 Microsoft Entra ID 同步

1. 在与 adVM 的远程桌面会话中，以管理员身份启动 Windows PowerShell。

2. 在 Windows PowerShell 控制台中，通过运行以下命令安装 MsOnline PowerShell 模块（系统提示时，在“需要继续使用 NuGet 提供程序”对话框中，键入“是”，然后按 Enter。）

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. 在 Windows PowerShell 控制台中运行以下命令，以连接到 Microsoft Entra 租户（系统提示时，使用 syncadmin 凭据登录）：****

    ```powershell
    Connect-MsolService
    ```

4. 在 Windows PowerShell 控制台中运行以下命令，以禁用 Microsoft Entra Connect 同步：

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. 在 Windows PowerShell 控制台中，通过运行以下命令验证操作是否成功：

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**注意**：结果应为 `False`。 如果不是这样，请等待一分钟，然后重新运行该命令。

    >**注意**：接下来，删除 Azure 资源
6. 关闭远程桌面会话。

7. 在 Azure 门户中，将“目录 + 订阅”筛选器设置为与在其中部署adVM Azure VM 的 Azure 订阅相关联的 Microsoft Entra 租户。********

8. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 

9. 在“Cloud Shell”窗格左上角的下拉菜单中，选择“PowerShell”，然后在出现提示时，单击“确认”。 

10. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. 关闭 Cloud Shell 窗格。

    >**** 备注：最后，删除 Microsoft Entra 租户
    
    >备注 2：删除租户将变得非常困难，因此绝不会意外或恶意地完成此过程。  这意味着在本实验室中删除租户通常不起作用。  虽然我们在此处提供了删除租户的步骤，但在完成本实验室时对此不作要求。 如果你在实际中需要删除租户，DOCS.Microsoft.com 上的文章可为你提供帮助。

12. 返回 Azure 门户，使用“目录 + 订阅”筛选器切换到 Microsoft Entra 租户 AdatumSync。********

13. 在 Azure 门户中，导航到“用户 - 所有用户”边栏选项卡，单击代表 syncadmin 用户帐户的条目，在“syncadmin - 个人资料”边栏选项卡中，单击“删除”，然后在提示确认时，单击“是”。

14. 重复相同的步骤以删除“aduser1”用户帐户和“本地目录同步服务帐户”。

15. 导航到 Microsoft Entra 租户的“AdatumSync - 概述”边栏选项卡，单击“管理租户”，选择“AdatumSync”目录的复选框，单击“删除”，在“删除租户‘AdatumSync’”边栏选项卡中，单击“获取删除 Azure 资源的权限”链接，在 Microsoft Entra 的“属性”边栏选项卡中，将“Azure 资源的访问管理”设置为“是”，然后单击“保存”。****************************************

    >**注意**：如果在删除时收到任何警告标志，例如“删除所有用户”，则继续删除已创建的用户，或者，如果警告标志显示“删除 LinkedIn 应用程序”，则单击警告消息并确认删除 LinkedIn 应用程序，所有的警告都需要处理，才能完成租户删除。

16. 从 Azure 门户注销并重新登录。 

17. 导航回“删除租户 ‘AdatumSync’”边栏选项卡，然后单击“删除”。

> 有关此任务的任何其他信息，请参阅 [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)