---
lab:
  title: 05 - Azure AD Privileged Identity Management
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: f0fc7615d2e2e3a26782366d7a630ed9984bd3f0
ms.sourcegitcommit: a8470295248a6363987bd5ea47154fe39f8535c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2022
ms.locfileid: "139703526"
---
# <a name="lab-05-azure-ad-privileged-identity-management"></a>实验室 05：Azure AD Privileged Identity Management
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

你需要创建一个概念证明，使用 Azure Privileged Identity Management (PIM) 来实现实时管理，并控制可以执行特权操作的用户数量。 具体要求是：

- 创建 aaduser2 Azure AD 用户到安全管理员角色的永久分配。 
- 将 Azure AD 用户 aaduser2 配置为有资格担任计费管理员和全局读取者角色。
- 配置全局读取器角色激活，以要求审批 aaduser3 Azure AD 用户
- 配置对全局读取者角色的访问评审并审查审核功能。

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

> 在继续之前，请确保已完成“实验室 04：MFA、条件访问和 AAD 标识保护。 你将需要 Azure AD 租户 AdatumLab500-04 以及用户帐户 aaduser1、aaduser2 和 aaduser3。

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：配置 PIM 用户和角色。
- 练习 2：激活 PIM 角色，无论经过审批与否。
- 练习 3：创建访问评审并审查 PIM 审核功能。

## <a name="azure-ad-privileged-identity-management-diagram"></a>Azure AD Privileged Identity Management 示意图

![image](https://user-images.githubusercontent.com/91347931/157522920-264ce57e-5c55-4a9d-8f35-e046e1a1e219.png)

## <a name="instructions"></a>说明

### <a name="exercise-1---configure-pim-users-and-roles"></a>练习 1 - 配置 PIM 用户和角色

#### <a name="estimated-timing-15-minutes"></a>预计用时：15 分钟

在本练习中，你将完成以下任务：

- 任务 1：使用户符合角色的条件。
- 任务 2：配置角色以要求审批才能激活和添加符合条件的成员。
- 任务 3：将用户永久分配至角色。 

#### <a name="task-1-make-a-user-eligible-for-a-role"></a>任务 1：确保用户有资格担任相关角色

在此任务中，你将使一个用户符合 Azure AD 目录角色的条件。

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：确保已登录 AdatumLab500-4b Azure AD 租户。 你可以使用“目录 + 订阅”筛选器在 Azure AD 租户之间切换。 务必以具有全局管理员角色的用户身份登录。
    
    >**注意**：如果你仍然未看到 AdatumLab500-04 条目，请单击“切换目录”链接，选择 AdatumLab500-04 行并单击“切换”按钮。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure AD Privileged Identity Management”，然后按 Enter 键。

3. 在“Privileged Identity Management”边栏选项卡的“管理”部分，单击“Azure AD 角色”  。

4. 在“AdatumLab500-04 \| 快速启动”边栏选项卡上的“管理”部分，单击“角色”  。

5. 在“AdatumLab500-04 \| 角色”边栏选项卡上，单击“+ 添加分配” 。

6. 在“添加分配”边栏选项卡的“选择角色”下拉列表中，选择“计费管理员”。

7. 单击“未选中成员”链接，在“选择成员”边栏选项卡上，单击“aaduser2”，然后单击“选择”。

8. 回到“添加分配”边栏选项卡，单击“下一步”。 

9. 确保将“分配类型”设置为“符合条件”，然后单击“分配”。
 
10. 返回到“AdatumLab500-04 \| 角色”边栏选项卡，在“管理”部分中，单击“分配”  。

11. 返回到“AdatumLab500-04 \| 分配”边栏选项卡，注意“符合条件的分配”、“活动分配”和“过期分配”选项卡   。

12. 验证“符合条件的分配”选项卡上的 aaduser2 是否显示为“计费管理员”。 

    >**注意**：登录期间，aaduser2 符合使用计费管理员角色的条件。 

#### <a name="task-2-configure-a-role-to-require-approval-to-activate-and-add-an-eligible-member"></a>任务 2：配置角色，使其需要审批才能激活和添加符合条件的成员

1. 在 Azure 门户中，导航回“Privileged Identity Management”边栏选项卡，并单击“Azure AD 角色” 。

2. 在“AdatumLab500-04 \| 快速启动”边栏选项卡上的“管理”部分，单击“角色”  。

3. 在“AdatumLab500-04 \| 角色”边栏选项卡上，单击“全局读取者”角色条目 。 

4. 在“全局读取者 \| 分配”边栏选项卡上，单击边栏选项卡工具栏中的“设置”图标，并查看该角色的配置设置，包括 Azure 多重身份验证要求 。

5. 单击 **“编辑”** 。

6. 在“激活”选项卡上，启用“需要审批才能激活”复选框。

7. 单击“选择审批者”，在“选择成员”边栏选项卡上，单击 aaduser3，然后单击“选择”。

8. 单击“下一步：分配”。

9. 清除“允许永久符合条件分配”复选框，将所有其他设置保留为默认值。

10. 单击“下一步: 通知”。

11. 查看“通知”设置，保留默认设置，然后单击“更新” 。

    >**注意**：现在，任何尝试使用全局读取者角色的人都需要 aaduser3 的审批。 

12. 在“全局读取者 \| 分配”边栏选项卡上，单击“+ 添加分配” 。

13. 在“添加分配”边栏选项卡上，单击“未选中成员”，在“选择成员”边栏选项卡上，单击“aaduser2”，然后单击“选择”。

14. 单击“下一步”。 

15. 确保“分配类型”为“符合条件”，并查看符合条件的持续时间设置。

16. 单击“分配”。

    >**注意**：用户 aaduser2 符合全局读取者角色的条件。 
 
#### <a name="task-3-give-a-user-permanent-assignment-to-a-role"></a>任务 3：将用户永久分配至角色。

1. 在 Azure 门户中，导航回“Privileged Identity Management”边栏选项卡，并单击“Azure AD 角色” 。

2. 在“AdatumLab500-04 \| 快速启动”边栏选项卡上的“管理”部分，单击“角色”  。

3. 在“AdatumLab500-04 \| 角色”边栏选项卡上，单击“+ 添加分配” 。

4. 在“添加分配”边栏选项卡的“选择角色”下拉列表中，选择“安全管理员”。

5. 在“添加分配”边栏选项卡上，单击“未选中成员”，在“选择成员”边栏选项卡上，单击“aaduser2”，然后单击“选择”。

6. 单击“下一步”。 

7. 查看“分配类型”设置，并单击“分配”。

8. 在“分配”页的“符合条件的分配”选项卡上，为“aaduser2”分配选择“更新”   。 选择“永久符合条件”，然后选择“保存” 。

    >**注意**：用户 aaduser2 现在永久符合安全管理员角色的条件。
    
### <a name="exercise-2---activate-pim-roles-with-and-without-approval"></a>练习 2 - 在经过审批和未经审批的情况下激活 PIM 角色

#### <a name="estimated-timing-15-minutes"></a>预计用时：15 分钟

在本练习中，你将完成以下任务：

- 任务 1：激活不需要审批的角色。 
- 任务 2：激活需要审批的角色。 

#### <a name="task-1-activate-a-role-that-does-not-require-approval"></a>任务 1：激活不需要审批的角色。

在此任务中，将激活一个不需要审批的角色。

1. 打开 InPrivate 浏览器窗口。

2. 在 InPrivate 浏览器窗口中，导航到 Azure 门户并使用 aaduser2 用户帐户登录。

    >**注意**：若要登录，需要提供 aaduser2 用户帐户的完全限定的名称，包括之前在本实验室中记录的 Azure AD 租户 DNS 域名。 该用户名的格式为 aaduser2@`<your_tenant_name>`.onmicrosoft.com，其中 `<your_tenant_name>` 占位符代表你唯一的 Azure AD 租户名称。 

3. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure AD Privileged Identity Management”，然后按 Enter 键。

4. 在“Privileged Identity Management”边栏选项卡的“任务”部分，单击“我的角色”  。

5. 应该会看到 aaduser2 的三个“合格角色”：“全局阅读者”、“安全管理员”和“计费管理员”。 

6. 在显示“计费管理员”角色条目的行中，单击“激活”。

7. 根据需要单击警告“需要附加验证。单击以继续”，并按照说明验证你的身份。

8. 在“激活 - 计费管理员”边栏选项卡的“原因”文本框中，键入提供激活理由的文本，然后单击“激活”。

    >**注意**：在 PIM 中激活角色时，最多可能需要 10 分钟才能使激活生效。 
    
    >**注意**：角色分配处于活动状态后，你的浏览器将会刷新（如果出现任何问题，只需注销并使用“aaduser2”用户帐户重新登录 Azure 门户）。

9. 导航回“Privileged Identity Management”边栏选项卡，然后在“任务”部分单击“我的角色”  。

10. 在“我的角色 \| Azure AD 角色”边栏选项卡上，切换到“活动分配”选项卡 。注意“计费管理员”角色是“已激活”状态。

    >**注意**：角色激活后，当达到“结束时间”（符合条件的持续时间）下的时间限制时，它将自动停用。

    >**注意**：如果提早完成管理员任务，可以手动停用角色。

11.  在“活动分配”列表中，在表示“计费管理员”角色的行中，单击“停用”链接。

12.  在“停用 - 计费管理员”边栏选项卡上，再次单击“停用”以进行确认。


#### <a name="task-2-activate-a-role-that-requires-approval"></a>任务 2：激活需要审批的角色。 

在此任务中，将激活需要审批的角色。

1. 在 InPrivate 浏览器窗口中，以用户“aaduser2”登录 Azure 门户，然后导航回“Privileged Identity Management \| 快速启动”边栏选项卡 。 

2. 在“Privileged Identity Management \| 快速启动”边栏选项卡的“任务”部分，单击“我的角色”  。

3. 在“我的角色 \| Azure AD 角色”边栏选项卡上，在“符合条件的分配”列表中显示“全局读取者”角色的行中，单击“激活”   。 

4. 在“激活 - 全局读取者”边栏选项卡的“原因”文本框中，键入提供激活理由的文本，然后单击“激活”。

5. 单击 Azure 门户工具栏中的通知图标，然后查看告知你请求正在等待审批的通知。

    >**注意**：作为特权角色管理员，你可以随时查看和取消请求。 

6. 在“我的角色 \| Azure AD 角色”边栏选项卡上，找到“安全管理员”角色，然后单击“激活”  。 

7. 单击警告“需要附加验证。单击以继续”。 

8. 按照说明验证你的身份。

    >**注意**：每个会话只需进行一次身份验证。 

9. 回到 Azure 门户界面后，在“激活 - 安全管理员”边栏选项卡的“原因”文本框中，键入提供激活理由的文本，然后单击“激活”。

    >**注意**：自动审批过程应该会完成。

10. 返回到“我的角色 \| Azure AD 角色”边栏选项卡，单击“活动分配”选项卡，注意“活动分配”列表包括“安全管理员”角色，但不包括“全局读取者”角色    。

    >**注意**：现在将审批“全局读取者”角色。

11. 以 aaduser2 的身份从 Azure 门户注销。

12. 以 aaduser3 的身份登录 Azure 门户。

    >**注意**：如果在使用任何一个用户帐户进行身份验证时遇到问题，可以使用你的用户帐户重置其密码或重新配置其登录选项以登录 Azure AD 租户。

13. 在 Azure 门户中，导航到“Azure AD Privileged Identity Management”（在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure AD Privileged Identity Management”，然后按 Enter 键）。

14. 在“Privileged Identity Management \| 快速启动”边栏选项卡的“任务”部分，单击“批准请求”  。

15. 在“批准请求 \| Azure AD 角色”边栏选项卡的“请求激活角色”部分中，选择表示由“aaduser2”向“全局读取者”角色发出的角色激活请求的条目的复选框   。

16. 单击“批准”。 在“审批请求”边栏选项卡上的“理由”文本框中键入激活原因，记下开始和结束时间，然后单击“确认”。 

    >**注意**：你还可以选择拒绝请求。

17. 以 aaduser3 的身份退出 Azure 门户。

18. 以 aaduser2 的身份登录 Azure 门户

19. 在 Azure 门户中，导航到“Azure AD Privileged Identity Management”（在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure AD Privileged Identity Management”，然后按 Enter 键）。

20. 在“Privileged Identity Management \| 快速启动”边栏选项卡的“任务”部分，单击“我的角色”  。

21. 在“我的角色 \| Azure AD 角色”边栏选项卡上，单击“活动分配”选项卡，并验证“全局阅读者”角色现在是否处于活动状态 。

    >**注意**：可能必须刷新页面才能查看活动分配的更新列表。

22. 注销并关闭 InPrivate 浏览器窗口。

> 结果：你已经练习过在经过审批和未经审批的情况下激活 PIM 角色。 

### <a name="exercise-3---create-an-access-review-and-review-pim-auditing-features"></a>练习 3 - 创建访问评审并查看 PIM 审核功能

#### <a name="estimated-timing-10-minutes"></a>预计用时：10 分钟

在本练习中，你将完成以下任务：

- 任务 1：在 PIM 中为 Azure AD 目录角色配置安全警报
- 任务 2：查看 PIM 警报、摘要信息和详细的审核信息

#### <a name="task-1-configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>任务 1：在 PIM 中为 Azure AD 目录角色配置安全警报

在此任务中，将减少与“过期”角色分配相关的风险。 为此，将通过创建 PIM 访问评审来确保分配的角色仍然有效。 具体来说，将审查全局读取者角色。 

1. 使用帐户登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：确保已登录 AdatumLab500-4b Azure AD 租户。 你可以使用“目录 + 订阅”筛选器在 Azure AD 租户之间切换。 务必以具有全局管理员角色的用户身份登录。
    
    >**注意**：如果你仍然未看到 AdatumLab500-04 条目，请单击“切换目录”链接，选择 AdatumLab500-04 行并单击“切换”按钮。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Azure AD Privileged Identity Management”，然后按 Enter 键。

3. 导航到“Privileged Identity Management”边栏选项卡。 

4. 在“Privileged Identity Management \| 快速启动”边栏选项卡的“管理”部分，单击“Azure AD 角色”  。

5. 在“AdatumLab500-04 \| 快速启动”边栏选项卡上的“管理”部分，单击“访问评审”  。

6. 在“AdatumLab500-04 \| 访问评审”边栏选项卡上，单击“新建” ：

7. 在“创建访问评审”边栏选项卡上，指定以下设置（将其他设置保留为默认值）： 

   |设置|值|
   |---|---|
   |评审名称|全局读取者评审|
   |开始日期|当天日期| 
   |频率|**一次**|
   |结束日期|本月末|
   |角色，选择特权角色|**全局读取者**|
   |审阅者|**所选用户**|
   |选择审阅者|你的帐户|

8. 在“创建访问评审”边栏选项卡上，单击“开始”：
 
    >**注意**：该评审大约需要一分钟才能部署完成并显示在“AdatumLab500-04 \| 访问评审”边栏选项卡上。 可能需要刷新网页。 评审状态为“活动”。 

9. 在“AdatumLab500-04 \| 访问评审”边栏选项卡上的“全局读取者评审”标题下，单击“全局读取者”条目  。 

10. 在“全局读取者评审”边栏选项卡上，检查“概述”页面，并注意“进度”图显示了“未评审”类别中的单个用户。 

11. 在“全局读取者评审”边栏选项卡的“管理”部分，单击“结果”。 请注意，aaduser2 被列为有权访问此角色。

12. 单击 aaduser2 行上的“查看”以查看详细的审核日志，其中包含代表涉及该用户的 PIM 活动的条目。

13. 导航回“AdatumLab500-04 \| 访问评审”边栏选项卡。

14. 在“AdatumLab500-04 \| 访问评审”边栏选项卡的“任务”部分中，单击“评审访问权限”，然后单击“全局读取者评审”条目   。 

15. 在“全局读取者评审”边栏选项卡上，单击 aaduser2 条目。 

16. 在“原因”文本框中输入审批理由，然后单击“批准”以维持当前角色的成员资格，或单击“拒绝”撤销该资格。 

17. 导航回“Privileged Identity Management”边栏选项卡，然后在“管理”部分，单击“Azure AD 角色”  。

18. 在“AdatumLab500-04 \| 快速启动”边栏选项卡上的“管理”部分，单击“访问评审”  。

19. 选择代表“全局读取者”评审的条目。 请注意“进度”图已更新以显示你的评审。 

#### <a name="task-2-review-pim-alerts-summary-information-and-detailed-audit-information"></a>任务 2：查看 PIM 警报、摘要信息和详细的审核信息。 

在此任务中，你将查看 PIM 警报、摘要信息和详细的审核信息。 

1. 导航回“Privileged Identity Management”边栏选项卡，然后在“管理”部分，单击“Azure AD 角色”  。

2. 在“AdatumLab500-04 \| 快速入门”边栏选项卡上，在“管理”部分，单击“警报”，然后单击“设置”   。

3. 在“警报设置”边栏选项卡上，查看预配置的警报和风险级别。 单击任何一个以获取更多详细信息。 

4. 返回到“AdatumLab500-04 \| 快速启动”边栏选项卡，然后单击“概述” 。 

5. 在“AdatumLab500-04 \| 概述”边栏选项卡上，查看有关角色激活、PIM 活动、警报和角色分配的摘要信息。

6. 在“AdatumLab500-04 \| 概述”边栏选项卡上，在“活动”部分，单击“资源审核”  。 

    >**注意**：审核历史记录可用于过去 30 天内所有特权角色分配和激活。

7. 注意，你可以检索详细信息，包括时间、请求者、操作、资源名称、范围、主目标和主题      。 

> 结果：你已配置了访问评审并检查了审核信息。 

**清理资源**

> 记得删除所有不再使用的新建 Azure 资源。 删除未使用的资源，确保不产生意外成本。 

1. 在 Azure 门户中，将“目录 + 订阅”筛选器设置为与你在其中部署 az500-04-vm1 Azure VM 的 Azure 订阅相关联的 Azure AD 租户。

    >**注意**：如果你未看到主 Azure AD 租户条目，请单击“切换目录”链接，选择主租户行并单击“切换”按钮。

2. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 如果出现提示，请单击“PowerShell”和“创建存储”。

3. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

4. 在 Cloud Shell 窗格中的 PowerShell 会话中，运行以下命令以删除你在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

5. 关闭 Cloud Shell 窗格。 

6. 返回 Azure 门户，使用“目录 + 订阅”筛选器切换到 AdatumLab500-04 Azure Active Directory 租户。

7. 导航到“AdatumLab500-04 Azure Active Directory”边栏选项卡的“管理”部分，并单击“许可证”  。

8. 在“许可证 | 概述”边栏选项卡上，单击“所有产品”，选中“Azure Active Directory Premium P2”的复选框并单击以打开  。

    >**注意**：在实验室 4 - 练习 2 - 任务 4 中，“向 Azure AD 用户分配 Azure AD Premium P2 许可证”是向用户“aaduser1”、“aaduser2”和“aaduser3”分配高级许可证，请确保从分配的用户中删除这些许可证 

9. 在“Azure Active Directory Premium P2 - 许可用户”边栏选项卡上，选中你为其分配了“Azure Active Directory Premium P2”许可证的用户帐户的复选框 。 单击顶部窗格中的“删除许可证”，在系统提示确认时，选择“是” 。

10. 在 Azure 门户中，导航到“用户 - 所有用户(预览)”边栏选项卡，单击表示“aaduser1”用户帐户的条目，在“aaduser1 - 个人资料”边栏选项卡上，单击“删除”，然后在系统提示确认时，选择“是”    。

11. 重复相同的步骤以删除你创建的其余用户帐户。

12. 导航到 Azure AD 租户的“AdatumLab500-04 - 概述”边栏选项卡，选择“管理租户”，然后在下一个屏幕上，选中“AdatumLab500-04”旁边的复选框，然后选择“删除”   。 在“删除租户‘AdatumLab500-04’”边栏选项卡上，选择“获取删除 Azure 资源的权限”链接，在 Azure Active Directory 的“属性”边栏选项卡上，将“Azure 资源的访问管理”设置为“是”，然后选择“保存”     。

13. 从 Azure 门户注销并重新登录。 

14. 导航回“删除目录‘AdatumLab500-04’”边栏选项卡，然后单击“删除” 。

    >**注意**：仍然无法删除租户，并引发错误“删除所有基于许可证和订阅”，这可能是由于已链接到租户的任何订阅所导致的。 此处，“免费高级 P2 许可证”可能会引发验证错误。 为解决此问题，可以从 M365 管理员>>“你的产品”中，以及从“业务应用商店”门户中，使用全局管理员 ID 删除 Premium P2 许可证的试用订阅 。 另请注意，删除租户需要更多时间。 检查订阅的结束日期，试用期结束后，重新访问 Azure Active Directory，然后尝试删除租户。    

> 有关此任务的任何其他信息，请参阅 [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)
