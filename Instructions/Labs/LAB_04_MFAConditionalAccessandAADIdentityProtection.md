---
lab:
  title: 04 - MFA、条件访问和 AAD 标识保护
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-04-mfa-conditional-access-and-aad-identity-protection"></a>实验室 04：MFA、条件访问和 AAD 标识保护
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to create a proof of concept of features that enhance Azure Active Directory (Azure AD) authentication. Specifically, you want to evaluate:

- Azure AD 多重身份验证
- Azure AD 条件访问
- Azure AD 标识保护

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：使用 Azure 资源管理器模板部署 Azure VM
- 练习 2：实现 Azure MFA
- 练习 3：实现 Azure AD 条件访问策略 
- 练习 4：实现 Azure AD 标识保护

## <a name="mfa---conditional-access---identity-protection-diagram"></a>MFA - 条件访问 - 标识保护示意图

![image](https://user-images.githubusercontent.com/91347931/157518628-8b4a9efe-0086-4ec0-825e-3d062748fa63.png)

## <a name="instructions"></a>说明

## <a name="lab-files"></a>实验室文件：

- \\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json
- \\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json 

### <a name="exercise-1-deploy-an-azure-vm-by-using-an-azure-resource-manager-template"></a>练习 1：使用 Azure 资源管理器模板部署 Azure VM

### <a name="estimated-timing-10-minutes"></a>预计用时：10 分钟

在本练习中，你将完成以下任务：

- 任务 1：使用 Azure 资源管理器模板部署 Azure VM。

#### <a name="task-1-deploy-an-azure-vm-by-using-an-azure-resource-manager-template"></a>任务 1：使用 Azure 资源管理器模板部署 Azure VM

In this task, you will create a virtual machine by using an ARM template. This virtual machine will be used in the last exercise for this lab. 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用帐户登录到 Azure 门户，该帐户在你用于此实验室的 Azure 订阅中具有所有者或参与者角色，并且在与该订阅关联的 Azure AD 租户中具有全局管理员角色。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“部署自定义模板” 。

    >**注意**：还可以选择“市场”列表中的“模板部署(使用自定义模板部署)” 。

3. 在“自定义部署”边栏选项卡中，单击“在编辑器中构建自己的模板”选项。

4. 在“编辑模板”边栏选项卡中，单击“加载文件”，找到 \\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json 文件并单击“打开”。

    >**注意**：查看模板的内容，注意它部署了一个托管 Windows Server 2019 数据中心的 Azure VM。

5. 在“编辑模板”边栏选项卡上，单击“保存”。

6. 返回“自定义部署”边栏选项卡，单击“编辑参数”。

7. 在“编辑参数”边栏选项卡中，单击“加载文件”，找到 \\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json 文件并单击“打开”。

    >**注意**：查看参数文件的内容，注意 adminUsername 和 adminPassword 值。

8. 在“编辑模板”边栏选项卡上，单击“保存”。

9. 在“自定义部署”边栏选项卡上，确保已配置以下设置（其他设置保留默认值）：

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You will need to create a unique password that will be used for creating VMs (virtual machines) for the rest of the course. The password must be at least 12 characters long and meet the defined complexity requirements (Password must have 3 of the following: 1 lower case character, 1 upper case character, 1 number, and 1 special character). <bpt id="p1">[</bpt>VM password requirements<ept id="p1">](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm-)</ept>. Please make a note of the password.

   |设置|值|
   |---|---|
   |订阅|将在此实验室中使用的 Azure 订阅的名称|
   |资源组|单击“新建”并键入名称“AZ500LAB04”|
   |位置|**（美国）美国东部**|
   |VM 大小|**Standard_D2s_v3**|
   |VM 名称|az500-04-vm1|
   |管理员用户名|**学生**|
   |管理员密码|**请创建自己的密码并记录它以供将来参考。系统将提示输入所需实验室访问权限的密码。**|
   |虚拟网络名称|az500-04-vnet1|

    >**Note**: To identify Azure regions where you can provision Azure VMs, refer to [**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

10. 单击“查看 + 创建”，然后单击“创建” 。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not wait for the deployment to complete but proceed to the next exercise. You will use the virtual machine included in this deployment in the last exercise of this lab.

> 结果：你已启动将在本实验室的下一个练习中使用的 Azure VM az500-vm1 的模板部署。


### <a name="exercise-2-implement-azure-mfa"></a>练习 2：实现 Azure MFA

### <a name="estimated-timing-30-minutes"></a>预计用时：30 分钟

在本练习中，你将完成以下任务

- 任务 1：创建一个新 Azure AD 租户。
- 任务 2：激活 Azure AD Premium P2 试用版。
- 任务 3：创建 Azure AD 用户和组。
- 任务 4：将 Azure AD Premium P2 许可证分配给 Azure AD 用户。
- 任务 5：配置 Azure MFA 设置。
- 任务 6：验证 MFA 配置

#### <a name="task-1-create-a-new-azure-ad-tenant"></a>任务 1：创建一个新 Azure AD 租户

在此任务中，你将创建新的 Azure AD 租户。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure Active Directory”，然后按 Enter 键。

2. 在显示当前 Azure AD 租户“概述”的边栏选项卡上，单击“管理租户”，然后在下一个屏幕上，单击“+ 创建”。

3. 在“创建租户”边栏选项卡的“基本信息”选项卡上，确保选中“Azure Active Directory”选项，然后单击“下一步:配置 >”。

4. 在“创建租户”边栏选项卡的“配置”选项卡上，指定以下设置：

   |设置|值|
   |---|---|
   |组织名称|AdatumLab500-04|
   |初始域名|由字母和数字组合组成的唯一名称|
   |国家或地区|**美国**|

    >你需要创建增强 Azure Active Directory (Azure AD) 身份验证的功能的概念证明。

5. 单击“查看 + 创建”，然后单击“创建”。
6. 在“帮助我们证明你不是机器人”边栏选项卡上添加验证码，然后单击“提交”按钮 。 

    >具体来说，你要评估： 


#### <a name="task-2-activate-azure-ad-premium-p2-trial"></a>任务 2：激活 Azure AD Premium P2 试用版

在此任务中，你将注册 Azure AD Premium P2 免费试用版。 

1. 在 Azure 门户的工具栏中，单击位于 Cloud Shell 图标的右侧的“目录 + 订阅”图标。 

2. 在“目录 + 订阅”边栏选项卡中，单击新建的租户“AdatumLab500-04”，然后单击“切换”按钮，将其设置为当前目录。

    >**注意**：如果 AdatumLab500-04 条目未出现在“目录 + 订阅”筛选器列表中，则可能需要你刷新浏览器窗口。

3. In the Azure portal, in the <bpt id="p1">**</bpt>Search resources, services, and docs<ept id="p1">**</ept> text box at the top of the Azure portal page, type <bpt id="p2">**</bpt>Azure Active Directory<ept id="p2">**</ept> and press the <bpt id="p3">**</bpt>Enter<ept id="p3">**</ept> key. On the <bpt id="p1">**</bpt>AdatumLab500-04<ept id="p1">**</ept> blade, in the <bpt id="p2">**</bpt>Manage<ept id="p2">**</ept> section, click <bpt id="p3">**</bpt>Licenses<ept id="p3">**</ept>.

4. 在“许可证 \| 概述”边栏选项卡的“管理”部分，单击“所有产品”，然后单击“+ 试用/购买”。

5. 在“激活”边栏选项卡上的 Azure AD Premium P2 部分，单击“免费试用版”，然后单击“激活”。


#### <a name="task-3-create-azure-ad-users-and-groups"></a>任务 3：创建 Azure AD 用户和组。

In this task, you will create three users: aaduser1 (Global Admin), aaduser2 (user), and aaduser3 (user). You will need each user's principal name and password for later tasks. 

1. 导航回 **AdatumLab500-04** Azure Active Directory 边栏选项卡，然后在“管理”部分，单击“用户”。

2. 在“用户 \| 所有用户”边栏选项卡上，单击“+ 新建用户” 。 

3. 在“新建用户”边栏选项卡上，确保选择“创建用户”选项，并指定以下设置（保留所有其他设置的默认值），然后单击“创建”：

   |设置|值|
   |---|---|
   |用户名|aaduser1|
   |名称|aaduser1|
   |密码|确保选中“自动生成密码”选项，然后单击“显示密码”|
   |组|**已选择 0 个组**|
   |角色|单击“用户”，再单击“全局管理员”，然后单击“选择”|
   |使用位置|**美国**|  

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record the full user name. You can copy its value by clicking the <bpt id="p1">**</bpt>Copy to clipboard<ept id="p1">**</ept> button on the right hand side of the drop-down list displaying the domain name. 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record the user's password. You will need this later in this lab. 

4. 返回到“用户 \| 所有用户”边栏选项卡上，单击“+ 新建用户” 。 

5. 在“新建用户”边栏选项卡中，确保已选择“创建用户”选项，并指定以下设置（将所有其他设置保留为默认值）：

   |设置|值|
   |---|---|
   |用户名|aaduser2|
   |名称|aaduser2|
   |密码|确保选中“自动生成密码”选项，然后单击“显示密码”|
   |组|**已选择 0 个组**|
   |角色|**用户**|
   |使用位置|**美国**|  

    >**注意**：记录完整的用户名和密码。

6. 返回到“用户 \| 所有用户”边栏选项卡上，单击“+ 新建用户” 。 

7. 单击“新建用户”，完成新用户配置设置，然后单击“创建”。

   |设置|值|
   |---|---|
   |用户名|aaduser3|
   |名称|aaduser3|
   |密码|确保选中“自动生成密码”选项，然后单击“显示密码”|
   |组|**已选择 0 个组**|
   |角色|**用户**|
   |使用位置|**美国**|  

    >**注意**：记录完整的用户名和密码。

    >**注意**：此时，你应会在“用户”页面上看到列出三名新用户。 
    
#### <a name="task-4-assign-azure-ad-premium-p2-licenses-to-azure-ad-users"></a>任务 4：将 Azure AD Premium P2 许可证分配给 Azure AD 用户

在此任务中，你将为每个用户分配 Azure Active Directory Premium P2 许可证。

1. 在“用户 \| 所有用户”边栏选项卡上，单击代表你的用户帐户的条目。 

2. 对于本实验室中的所有资源，我们使用“美国东部”区域。

3. 导航回 **AdatumLab500-04** Azure Active Directory 边栏选项卡的“管理”部分，并单击“许可证”。

4. 在“许可证 \| 概述”边栏选项卡上，单击“所有产品”，选中“Azure Active Directory Premium P2”复选框，然后单击“+ 分配”。

5. 在“分配许可证”边栏选项卡上，单击“+添加用户和组”。

6. 在“用户”边栏选项卡上，选择“aaduser1”、“aaduser2”、“aaduser3”和你的用户帐户，然后单击“选择”。

7. 返回到“分配许可证”边栏选项卡，单击“分配选项”，确保已启用所有选项，单击“查看 + 分配”，然后单击“分配”   。

8. 请与讲师确认这是课堂上所使用的区域。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: At this point, you assigned Azure Active Directory Premium P2 licenses to all user accounts you will be using in this lab. Be sure to sign out and then sign back in. 

#### <a name="task-5-configure-azure-mfa-settings"></a>任务 5：配置 Azure MFA 设置。

在此任务中，你将配置 MFA 并为 aaduser1 启用 MFA。 

1. 在 Azure 门户中，导航回 AdatumLab500-04 Azure Active Directory 租户边栏选项卡。

    >**注意**：确保你使用的是 AdatumLab500-04 Azure AD 租户。

2. 在 **AdatumLab500-04** Azure Active Directory 租户边栏选项卡上的“管理” 部分，单击“安全性”。

3. 在“安全 \| 开始”边栏选项卡上的“管理”部分，单击“MFA”。

4. 在“多重身份验证 \| 开始”边栏选项卡上，单击“其他基于云的 MFA 设置”链接。 

    >**注意**：将会打开一个新的浏览器选项卡，显示“多重身份验证”页面。

5. On the <bpt id="p1">**</bpt>multi-factor authentication<ept id="p1">**</ept> page, click the <bpt id="p2">**</bpt>service settings<ept id="p2">**</ept> tab. Review <bpt id="p3">**</bpt>verification options<ept id="p3">**</ept>. Note that <bpt id="p1">**</bpt>Text message to phone<ept id="p1">**</ept>, <bpt id="p2">**</bpt>Notification through mobile app<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>Verification code from mobile app or hardware token<ept id="p3">**</ept> are enabled. Click <bpt id="p1">**</bpt>Save<ept id="p1">**</ept> and then click <bpt id="p2">**</bpt>close<ept id="p2">**</ept>.

6. 切换到“用户”选项卡，依次单击 **aaduser1** 条目和“启用”链接，然后在出现提示时单击“启用多重身份验证”。

7. 注意，**aaduser1** 的“多重身份验证状态”列现在**已启用**。

8. 单击 aaduser1 并注意，此时你还有“强制”选项。 

    >**注意**：将用户状态从“已启用”更改为“已强制”仅影响不支持 Azure MFA 的旧版 Azure AD 集成应用，并且一旦状态更改为“已强制”，则需要使用应用密码。

9. 选择 aaduser1 条目后，单击“管理用户设置”并查看可用选项： 

   - 要求所选用户再次提供联系方法。

   - 删除所选用户生成的所有现有应用密码。

   - 在所有记住的设备上恢复多重身份验证。

10. 单击“取消”，然后切换回在 Azure 门户中显示“多重身份验证 \| 开始”的浏览器标签页。

11. 在“设置”部分，单击“欺诈警报”。

12. 在“多重身份验证 \| 欺诈警报”边栏选项卡上，配置以下设置：

   |设置|值|
   |---|---|
   |允许用户提交欺诈警报|**开**|
   |自动阻止举报欺诈的用户|**开**|
   |初次问候时报告欺诈的代码|**0**|

13. 单击“保存”

    >**注意**：至此，你已经为 aaduser1 启用了 MFA，并设置了欺诈警报设置。 

14. Navigate back to the <bpt id="p1">**</bpt>AdatumLab500-04<ept id="p1">**</ept> Azure Active Directory tenant blade, in the <bpt id="p2">**</bpt>Manage<ept id="p2">**</ept> section, click <bpt id="p3">**</bpt>Properties<ept id="p3">**</ept>, next click the <bpt id="p4">**</bpt>Manage Security defaults<ept id="p4">**</ept> link at the bottom of the blade, on the <bpt id="p5">**</bpt>Enable Security Defaults<ept id="p5">**</ept> blade, click <bpt id="p6">**</bpt>No<ept id="p6">**</ept>. Select <bpt id="p1">**</bpt>My Organization is using Conditonal Access<ept id="p1">**</ept> as the reason and and then click <bpt id="p2">**</bpt>Save<ept id="p2">**</ept>.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Ensure that you are signed-in to the <bpt id="p2">**</bpt>AdatumLab500-04<ept id="p2">**</ept> Azure AD tenant. You can use the <bpt id="p1">**</bpt>Directory + subscription<ept id="p1">**</ept> filter to switch between Azure AD tenants. Ensure you are signed in as a user with the Global Administrator role in the Azure AD tenant.

#### <a name="task-6-validate-mfa-configuration"></a>任务 6：验证 MFA 配置

在此任务中，你将通过测试 aaduser1 用户帐户的登录来验证 MFA 配置。 

1. 打开 InPrivate 浏览器窗口。

2. 导航到 Azure 门户，并使用 aaduser1 用户帐户登录。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: To sign in you will need to provide a fully qualified name of the <bpt id="p2">**</bpt>aaduser1<ept id="p2">**</ept> user account, including the Azure AD tenant DNS domain name, which you recorded earlier in this lab. This user name is in the format aaduser1@<ph id="ph1">`&lt;your_tenant_name&gt;`</ph>.onmicrosoft.com, where <ph id="ph2">`&lt;your_tenant_name&gt;`</ph> is the placeholder representing your unique Azure AD tenant name. 

3. 出现提示时，在“需要更多信息”对话框中，单击“下一步”。

    >**注意**：浏览器会话将重定向到“附加安全验证”页面。

4. 在“保护帐户安全”页面，选择“我想设置其他方法”链接，在“你想要使用哪种方法?”下拉列表中，选择“电话”，并选择“确认”。

5. 在“保护帐户安全”页面，选择你的国家/地区或区域，在“输入电话号码”区域中键入你的手机号码，确保选中“通过短信向我发送代码”选项，并单击“下一步”。
 
6. 在“保护帐户安全”页面，键入你的手机收到的短信中的代码，并单击“下一步”。

7. 在“保护帐户安全”页面，确保验证成功并单击“下一步”。

8. On the <bpt id="p1">**</bpt>Keep your account secure<ept id="p1">**</ept> page, click <bpt id="p2">**</bpt>I want to use a different method<ept id="p2">**</ept>, select <bpt id="p3">**</bpt>Email<ept id="p3">**</ept> from the drop down list, click <bpt id="p4">**</bpt>Confirm<ept id="p4">**</ept>, provide the  email address you intend to use, and click <bpt id="p5">**</bpt>Next<ept id="p5">**</ept>. Once you receive the corresponding email, identify the code in the email body, provide it, and then click <bpt id="p1">**</bpt>Done<ept id="p1">**</ept>.

9. When prompted, change your password. Make sure to record the new password.

10. 验证你是否已成功登录 Azure 门户。

11. 以 **aaduser1** 身份注销并关闭 InPrivate 浏览器窗口。

> 结果：你已经创建了新的 AD 租户，配置了 AD 用户和 MFA，并为该用户测试了 MFA 体验。 


### <a name="exercise-3-implement-azure-ad-conditional-access-policies"></a>练习 3：实现 Azure AD 条件访问策略 

### <a name="estimated-timing-15-minutes"></a>预计用时：15 分钟

在本练习中，你将完成以下任务 

- 任务 1：配置条件访问策略。
- 任务 2：测试条件访问策略。

#### <a name="task-1---configure-a-conditional-access-policy"></a>任务 1 - 配置条件访问策略。 

在此任务中，你将查看条件访问策略设置，并创建登录 Azure 门户时需要 MFA 的策略。 

1. 在 Azure 门户中，导航回 AdatumLab500-04 Azure Active Directory 租户边栏选项卡。

2. 在“AdatumLab500-04”边栏选项卡上的“管理”部分，单击“安全”。

3. 在“安全 \| 开始”边栏选项卡的“保护”部分，单击“条件访问”。

4. 在“条件访问 \| 策略”边栏选项卡中，单击“+ 新建策略”，然后从下拉列表中选择“新建策略”。 

5. 在“新建”边栏选项卡上，配置以下设置：

   - 在“名称”文本框中，键入“AZ500Policy1”
    
   - Click <bpt id="p1">**</bpt>Users or workload identities selected<ept id="p1">**</ept>. On the right side under the What does this policy apply to &gt;&gt; Users and groups &gt;&gt; Include &gt;&gt; Enable <bpt id="p1">**</bpt>Select users and groups<ept id="p1">**</ept> &gt;&gt; select the <bpt id="p2">**</bpt>Users and Groups<ept id="p2">**</ept> checkbox, on the <bpt id="p3">**</bpt>Select<ept id="p3">**</ept> blade, click <bpt id="p4">**</bpt>aaduser2<ept id="p4">**</ept>, and click <bpt id="p5">**</bpt>Select<ept id="p5">**</ept>.
    
   - 单击“云应用或操作”，再单击“选择应用”，然后在“选择”边栏选项卡上，单击“Microsoft Azure 管理”，然后单击“选择”。 

    >**注意**：查看此策略会影响访问 Azure 门户的警告。
    
   - 单击“条件”，再单击“登录风险”，然后在“登录风险”边栏选项卡上，查看风险级别，但不要进行任何更改，然后关闭“登录风险”边栏选项卡。
    
   - 单击“设备平台”，查看可以包含的设备平台，然后单击“完成”。
    
   - 单击“位置”，查看位置选项，而不做任何更改。
    
   - 单击“访问控制”部分的“授权” ，然后在“授权”边栏选项卡上，选中“需要多重身份验证”复选框，然后单击“选择”
    
   - 将“启用策略”设置为“开启”。

6. 在“新建”边栏选项卡中，单击“创建”。******** 

    >**注意**：此时，你已拥有需要 MFA 以登录 Azure 门户的条件访问策略。 

#### <a name="task-2---test-the-conditional-access-policy"></a>任务 2 - 测试条件访问策略。

In this task, you will sign in to the Azure portal as <bpt id="p1">**</bpt>aaduser2<ept id="p1">**</ept> and verify MFA is required. You will also delete the policy before continuing on to the next exercise. 

1. 打开 InPrivate Microsoft Edge 窗口。

2. 在新的浏览器窗口中，导航到 Azure 门户并使用 aaduser2 用户帐户登录。

3. 出现提示时，在“需要更多信息”对话框中，单击“下一步”。

    >**注意**：浏览器会话将重定向到“保护帐户安全”页面。
    
4. 在“保护帐户安全”页面，选择“我想设置其他方法”链接，在“你想要使用哪种方法?”下拉列表中，选择“电话”，并选择“确认”。

5. 在“保护帐户安全”页面，选择你的国家/地区或区域，在“输入电话号码”区域中键入你的手机号码，确保选中“通过短信向我发送代码”选项，并单击“下一步”。

6. 在“保护帐户安全”页面，键入你的手机收到的短信中的代码，并单击“下一步”。

7. 在“保护帐户安全”页面，确保验证成功并单击“下一步”。

8. 在“保护帐户安全”页面，单击“完成”。

9. When prompted, change your password. Make sure to record the new password.

10. 验证你是否已成功登录 Azure 门户。

11. 以“aaduser2”身份注销并关闭 InPrivate 浏览器窗口。

    >**注意**：现在已经验证了 aaduser2 登录 Azure 门户时，新创建的条件访问策略会强制执行 MFA。

12. 返回显示 Azure 门户的浏览器窗口，导航回“AdatumLab500-04”Azure Active Directory 租户边栏选项卡。

13. 在“AdatumLab500-04”边栏选项卡上的“管理”部分，单击“安全”。

14. 在“安全 \| 开始”边栏选项卡的“保护”部分，单击“条件访问”。

15. 在“条件访问 \| 策略”边栏选项卡上，单击 AZ500Policy1 旁边的省略号，然后单击“删除”，在系统提示确认时，单击“是”。

    >**注意**：结果：在本练习中，你将实现要求在用户登录 Azure 门户时进行 MFA 的条件访问策略。 

>结果：你已配置并测试了 Azure AD 条件访问。

### <a name="exercise-4-implement-azure-ad-identity-protection"></a>练习 4：实现 Azure AD 标识保护

### <a name="estimated-timing-30-minutes"></a>预计用时：30 分钟

在本练习中，你将完成以下任务 

- 任务 1：在 Azure 门户中查看 Azure AD 标识保护选项
- 任务 2：配置用户风险策略
- 任务 3：配置登录风险策略
- 任务 4：根据 Azure AD 标识保护策略模拟风险事件 
- 任务 5：查看 Azure AD 标识保护报告

#### <a name="task-1-enable-azure-ad-identity-protection"></a>任务 1：启用 Azure AD 标识保护

在此任务中，你将在 Azure 门户中查看 Azure AD 标识保护选项。 

1. 如果需要，登录到 Azure 门户 `https://portal.azure.com/`。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Ensure that you are signed-in to the <bpt id="p2">**</bpt>AdatumLab500-04<ept id="p2">**</ept> Azure AD tenant. You can use the <bpt id="p1">**</bpt>Directory + subscription<ept id="p1">**</ept> filter to switch between Azure AD tenants. Ensure you are signed in as a user with the Global Administrator role in the Azure AD tenant.

2. 在“AdatumLab500-04”边栏选项卡上的“管理”部分，单击“安全”。

3. 在“安全 \| 开始”边栏选项卡的“保护”部分，单击“标识保护”。

4. 在“标识保护 \| 概述”边栏选项卡上，查看“检测到的新风险用户”和“检测到的新风险登录”图表以及其他有关风险用户的信息  。 

#### <a name="task-2-configure-a-user-risk-policy"></a>任务 2：配置用户风险策略

在此任务中，创建一个用户风险策略。 

1. 在“标识保护 \| 概述”边栏选项卡的“保护”部分，单击“用户风险策略”

2. 使用以下设置配置用户风险修正策略： 

   - 单击“用户”；在“用户”边栏选项卡的“包含”选项卡上，确保选中“所有用户”选项。

   - 在“用户”边栏选项卡上，切换到“排除”选项卡，单击“选择排除的用户”，选择用户帐户，然后单击“选择”。 

   - 单击“用户风险”；在“用户风险”边栏选项卡上，选择“低及以上”，然后单击“完成”。 

   - 单击“访问权限”；在“访问权限”边栏选项卡上，确保选中“允许访问”选项和“需要更改密码”复选框，然后单击“完成”。

   - 将“强制执行策略”设置为“开启”，然后单击“保存”。

#### <a name="task-3-configure-sign-in-risk-policy"></a>任务 3：配置登录风险策略

在此任务中，你将配置登录风险策略。 

1. 在“标识保护 \| 用户风险策略”边栏选项卡的“保护”部分，单击“登录风险策略”

2. 使用以下设置配置“登录风险修正策略”： 

   - 单击“用户”；在“用户”边栏选项卡的“包含”选项卡上，确保选中“所有用户”选项。

   - 单击“登录风险”；在“登录风险”边栏选项卡上，选择“中及以上”，然后单击“完成”。 

   - 单击“访问权限”；在“访问权限”边栏选项卡上，确保选中“允许访问”选项和“需要多重身份验证”复选框，然后单击“完成”。

   - 将“强制执行策略”设置为“开启”，然后单击“保存”。

#### <a name="task-4-simulate-risk-events-against-the-azure-ad-identity-protection-policies"></a>任务 4：根据 Azure AD 标识保护策略模拟风险事件 

> Before you start this task, ensure that the template deployment you started in Exercise 1 has completed. The deployment includes an Azure VM named <bpt id="p1">**</bpt>az500-04-vm1<ept id="p1">**</ept>. 

1. 在 Azure 门户中，将“目录 + 订阅”筛选器设置为与你在其中部署 az500-04-vm1 Azure VM 的 Azure 订阅相关联的 Azure AD 租户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键  。

3. 在“虚拟机”边栏选项卡上，单击“az500-04-vm1”条目。 

4. 在“az500-04-vm1”边栏选项卡，单击“连接”，然后在下拉菜单中单击“RDP”。 

5. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>az500-04-vm1<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

   |设置|值|
   |---|---|
   |用户名|**学生**|
   |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >**注意**：等待加载远程桌面会话和服务器管理器。  

    >**注意**：在到 az500-04-vm1 Azure VM 的远程桌面会话中，执行以下步骤。 

6. 在“服务器管理器”中，单击“本地服务器”，然后单击“IE 增强型安全配置”。

7. 在“Internet Explorer 增强的安全配置”对话框中，将两个选项都设置为“关闭”，然后单击“确定”。

8. 启动 IE 浏览器，单击工具栏中的齿轮图标，在下拉菜单中，单击“安全”，然后单击“InPrivate 浏览”。

9. 在 InPrivate Internet Explorer 窗口中，导航到 ToR 浏览器项目（网址为 https://www.torproject.org/projects/torbrowser.html.en）。

10. 使用默认设置下载并安装 Windows 版本的 ToR 浏览器。 

11. 安装完成后，启动 ToR 浏览器，使用初始页面上的“连接”选项，然后浏览到应用程序访问面板（网址为 https://myapps.microsoft.com）。

12. 出现提示时，尝试使用 **aaduser3** 帐户登录。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You will be presented with the message <bpt id="p2">**</bpt>Your sign-in was blocked<ept id="p2">**</ept>. This is expected, since this account is not configured with multi-factor authentication, which is required due to increased sign-in risk associated with the use of ToR Browser.

13. 使用“注销并使用另一个帐户登录”选项或选择后退箭头，可以使用之前在本实验室中为多重身份验证创建和配置的帐户 aaduser1 登录。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This time, you will be presented with the <bpt id="p2">**</bpt>Suspicious activity detected<ept id="p2">**</ept> message. Again, this is expected, since this account is configured with multi-factor authentiation. Considering the increased sign-in risk associated with the use of ToR Browser, you will have to use multi-factor authentication.

14. 使用“验证”选项并指定是否要通过短信或电话来验证你的身份。

15. 完成验证并确保你已成功登录到应用程序访问面板。

16. 关闭 RDP 会话。 

    >**注意**：到现在，你已尝试了两种不同的登录。接下来要查看 Azure 标识保护报告。

#### <a name="task-5-review-the-azure-ad-identity-protection-reports"></a>任务 5：查看 Azure AD 标识保护报告

在此任务中要查看从 ToR 浏览器登录中生成的 Azure AD 标识保护报告。

1. 返回 Azure 门户，使用“目录 + 订阅”筛选器切换到 AdatumLab500-04 Azure Active Directory 租户。

2. 在“AdatumLab500-04”边栏选项卡上的“管理”部分，单击“安全”。

3. 在“安全 \| 开始”边栏选项卡的“报告”部分，单击“风险用户”。 

4. 查看报告并确定引用 aaduser3 用户帐户的条目。

5. 在“安全 \| 开始”边栏选项卡的“报告”部分，单击“风险登录”。 

6. 查看报告并确定使用 aaduser3 用户帐户登录的对应条目。

7. 在“报告”下，单击“风险检测”。

8. 查看报告并确定表示从 ToR 浏览器生成的匿名 IP 地址登录的条目。 

 >**注意**：在报告中显示风险的过程可能需要 10-15 分钟。

> **Result**：你已通过模拟风险事件启用了 Azure AD 标识保护，配置了用户风险策略和登录风险策略，并验证了 Azure AD 标识保护配置。

**清理资源**

> 我们需要删除你不再使用的标识保护资源。 

使用以下步骤禁用 AdatumLab500-04 Azure AD 租户中的标识保护策略。

1. 在 Azure 门户中，导航回 AdatumLab500-04 Azure Active Directory 租户边栏选项卡。

2. 在“AdatumLab500-04”边栏选项卡上的“管理”部分，单击“安全”。

3. 在“安全 \| 开始”边栏选项卡的“保护”部分，单击“标识保护”。

4. 在“标识保护 \| 概述”边栏选项卡上，单击“用户风险策略”。

5. 在“标识保护 \| 用户风险策略”边栏选项卡上，将“强制执行策略”设置为“关”，然后单击“保存”。

6. 在“标识保护 \| 用户风险策略”边栏选项卡上，单击“登录风险策略”

7. 在“标识保护 \| 登录风险策略”边栏选项卡上，将“强制执行策略”设置为“关”，然后单击“保存”。

使用以下步骤停止之前在实验室中预配的 Azure VM。

1. 在 Azure 门户中，将“目录 + 订阅”筛选器设置为与你在其中部署 az500-04-vm1 Azure VM 的 Azure 订阅相关联的 Azure AD 租户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键  。

3. 在“虚拟机”边栏选项卡上，单击“az500-04-vm1”条目。 
 
4. 在“az500-04-vm1”边栏选项卡上，单击“停止”，然后在提示确认时，单击“确定” 

>  不要删除本实验室中预配的任何资源，因为 PIM 实验室依赖于这些资源。
