---
lab:
  title: 03 - 针对 Microsoft Entra 已验证 ID 配置租户
  module: Module 01 - Manage Identity and Access
---
# 实验室 03：针对 Microsoft Entra 已验证 ID 配置租户
# 学生实验室手册

## 实验室方案

# 为租户配置 Microsoft Entra 验证 ID

>[!Note] 
> Azure Active Directory 可验证凭据现已是 Microsoft Entra 验证的 ID，并且属于 Microsoft Entra 系列产品。 详细了解标识解决方案 [Microsoft Entra](https://aka.ms/EntraAnnouncement) 系列，并开始使用[统一 Microsoft Entra 管理中心](https://entra.microsoft.com)。

Microsoft Entra 验证 ID 是一种有助于保护组织的分散式身份识别解决方案。 使用此服务可以颁发和验证凭据。 颁发者可以使用验证 ID 服务颁发其自定义可验证凭据。 验证者可以使用该服务的免费 REST API 在应用和服务中轻松请求和接受可验证凭据。 在这两种情况下，都需要对 Azure AD 租户进行配置，以颁发自己的可验证凭据，或验证由第三方颁发的用户可验证凭据的呈现方式。 如果你既是颁发者又是验证者，则可以使用单个 Azure AD 租户来颁发你自己的可验证凭据并验证其他人的凭据。

本教程介绍如何配置 Azure AD 租户，以便它使用可验证凭据服务。

具体来说，你将学习如何：

> - 创建 Azure Key Vault 实例。
> - 设置验证 ID 服务。
> - 在 Azure AD 中注册应用程序。
> - 验证域对分散式标识符 (DID) 的所有权

下图显示了验证 ID 体系结构以及所配置的组件。

![图像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8d4d01c2-3110-421a-91a8-7b052bc8d793)


## 先决条件

- 请确保你具有要配置的目录的全局管理员或身份验证策略管理员权限。 如果你不是全局管理员，则需要具有应用程序管理员权限才能完成应用注册，包括授予管理员同意。
- 对于您在其中部署 Azure Key Vault 的 Azure 订阅或资源组，请确保你具有参与者角色。

## 创建 Key Vault

Azure Key Vault 是一种云服务，可实现对机密和密钥的安全存储和访问。 验证 ID 服务将公钥和私钥存储在 Azure Key Vault 中。 这些密钥用来对凭据进行签名和验证。

### 使用 Azure 门户创建 Azure 密钥保管库。

1. 启动浏览器会话并登录到 [Azure 门户](https://portal.azure.com/)。
   
2. 在 Azure 门户搜索框中，输入“密钥保管库”****。

3. 从结果列表中选择“密钥保管库”。

4. 在“密钥保管库”部分，选择“创建”****。

5. 在“创建密钥保管库”的“基本信息”选项卡中，输入或选择以下信息：********
   
   |设置|值|
   |---|---|
   |**项目详细信息**|
   |订阅|选择订阅。|
   |资源组|输入 azure-rg-1****。 选择“确定”|
   |**实例详细信息**|
   |密钥保管库名称|输入 Contoso-vault2****。|
   |区域|选择“美国东部”|
   |定价层|系统默认值：Standard****|
   |保留已删除保管库的天数|系统默认值：90****|

6. 选择“查看 + 创建”选项卡或页面底部的“查看 + 创建”按钮****。
  
7. 选择**创建**。

记下以下两个属性：

* **保管库名称**：在本示例中，此项为 **Contoso-Vault2**。 你将在其他步骤中使用此名称。
* 保管库 URI：在此示例中，保管库 URI 为 `https://contoso-vault2.vault.azure.net/`。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户有权对这个新保管库执行操作。

>[!NOTE]
>默认情况下，仅创建保管库的帐户具有访问权限。 验证 ID 服务需要密钥保管库的访问权限。 必须为密钥保管库配置访问策略，允许配置期间使用的帐户创建和删除密钥。 配置期间使用的帐户还需要具有签名权限，以便它可以创建验证 ID 的域绑定。 如果在测试时使用的是同一帐户，请将默认策略修改为除了授予保管库创建者默认访问权限以外，授予帐户登录权限。

### 为密钥保管库设置访问策略

Key Vault 定义指定的安全主体是否可以对 Key Vault 机密和密钥执行操作。 在密钥保管库中，为验证 ID 服务管理员帐户以及你创建的请求服务 API 主体设置访问策略。
创建密钥保管库后，可验证凭据可生成一组用于提供消息安全性的密钥。 这些密钥存储在密钥保管库中。 使用密钥集更新和恢复可验证凭据并对其进行签名。

### 为验证 ID 管理员用户设置访问策略

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 转到用于本教程的密钥保管库。

3. 在“设置”下，选择“访问策略” 。

4. 在“添加访问策略”的“用户”下，选择用于执行本教程操作的帐户 。

5. 对于“密钥权限”，请验证是否已选择以下权限：获取、创建、删除和签名。 默认情况下，“创建”和“删除”已启用。 “签名”应该是唯一需要更新的密钥权限。

      ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/7c8a92ea-24f1-41e6-9656-869e8486af72)


6. 若要保存更改，请选择“保存”。

## 设置验证 ID

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b4b857a2-24b8-4c3f-9f5c-43d23b58427f)


若要设置验证 ID，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“*已验证 ID*”。 然后选择“验证 ID”。

3. 从左侧菜单中选择“设置”。

4. 从中间菜单中选择“定义组织设置”

5. 提供以下信息来设置组织：

    1. 组织名称：输入名称，以便在验证 ID 中引用你的企业。 你的客户看不到此名称。

    1. **受信任的域**：输入的域将添加到分散式身份识别 (DID) 文档中的服务终结点。 域将你的 DID 绑定到用户可能了解的你业务中的内容。 Microsoft Authenticator 和其他数字钱包使用该信息来验证你的 DID 是否与你的域相关联。 如果钱包可以验证 DID，则会显示已验证的符号。 如果钱包无法验证 DID，则会通知用户凭据是由它无法验证的组织颁发的。

        >[!IMPORTANT]
        > 域不能重定向。 否则，“DID”和“域”将无法链接。 请确保对域使用 HTTPS。 例如：`https://did.woodgrove.com`。

    1. **密钥保管库**：选择之前创建的密钥保管库。

    1. 在“高级”下，可以选择要用于租户的信任系统。 可以从“Web”或“ION”中进行选择。 “Web”表示租户使用 [did:web](https://w3c-ccg.github.io/did-method-web/) 作为 did 方法，“ION”表示使用 [did:ion](https://identity.foundation/ion/)。

        >[!IMPORTANT]
        > 更改信任系统的唯一方法是选择退出验证 ID 服务然后重新加入。

1. 选择“保存”。  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b191676e-03e3-423f-aa28-1e3d2887ea07)


### 为验证 ID 服务主体设置访问策略

在上一步中设置验证 ID 时，Azure 密钥保管库中的访问策略会自动更新，为验证 ID 的服务主体提供所需权限。  
如果需要手动重置权限，访问策略应如下所示。

| Service Principal | AppId | 密钥权限 |
| -------- | -------- | -------- |
| 可验证凭据服务 | bb2a64ee-5d29-4b07-a491-25806dc854d3 | 获取、签名 |
| 可验证凭据服务请求 | 3db474b9-6a0c-4840-96ac-1fceb342124f | 签名 |



![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/896b8ea0-b88b-43b8-a93b-4771defedfde)


## 在 Azure AD 中注册应用程序

当应用程序想要调用 Microsoft Entra 已验证 ID 时，它需要获取访问令牌，以便可以颁发或验证凭据。 若要获取访问令牌，必须注册应用程序并为已验证 ID 请求服务授予 API 权限。 例如，对 Web 应用程序使用以下步骤：

1. 使用管理帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 如果有权访问多个租户，请选择“目录 + 订阅”。 然后，搜索并选择“Azure Active Directory”。

1. 在“管理”下，选择“应用注册” > “新建注册”  。  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/03ca3d13-5564-4ce2-b42c-f8333bc97fb5)


1. 输入应用程序的显示名称。 例如：verifiable-credentials-app。

1. 对于“支持的帐户类型”，选择“仅此组织目录中的帐户(仅默认目录 - 单租户)” 。

1. 选择“注册”以创建应用程序。

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d0a15737-c8a9-4522-990d-1cf6bc12cc1e)


### 授予获取访问令牌的权限

在此步骤中，你将向“可验证凭据服务请求”服务主体授予权限。

若要添加所需权限，请执行以下步骤：

1. 停留在 verifiable-credentials-app 应用程序详细信息页面上。 选择“API 权限” > “添加权限” 。

   ![图像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/adf97022-2081-4273-8d61-f2b53628e989)

1. 选择“我的组织使用的 API”。

1. 搜索“可验证凭据服务请求”服务主体并将其选中。

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/01691eb2-ab7f-4778-9911-342eb9350f99)

1. 选择“应用程序权限”，然后展开 VerifiableCredential.Create.All 。

   ![图像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/3c5e008e-2ba5-417a-90ff-22e8f622ad34)


1. 选择“添加权限”。

1. 选择“为 \<your tenant name\> 授予管理员许可”****。

如果你希望将范围隔离到不同的应用程序，则可以选择分别授予发布和演示权限。

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/640def45-e666-423e-bf69-598e8980b125)

## 注册分散式 ID 并验证域所有权

设置 Azure 密钥保管库且服务具有签名密钥后，必须完成设置中的步骤 2 和 3。

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/40e10177-b025-4d10-b16a-d66c06762c3f)

1. 在 Azure 门户中，导航到“验证 ID 服务”。  
1. 从左侧菜单中选择“设置”。
1. 从中间菜单中，选择“验证域所有权”****。

# 验证域对分散式标识符 (DID) 的所有权

## 验证域所有权并分发 did-configuration.json 文件

域必须由你控制，且其格式应为 `https://www.example.com/`。 

1. 在 Azure 门户中，导航到“VerifiedID”页。

1. 选择“**设置**”，然后选择“**验证域所有权**”，并为域选择“**验证**”

1. 复制或下载 `did-configuration.json` 文件。

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/342fa12f-2d0d-40cf-a2f9-8796a86f0824)

1. 将 `did-configuration.json` 文件托管在指定的位置。 示例：如果指定了域 `https://www.example.com`，则需要在此 URL `https://www.example.com/.well-known/did-configuration.json` 上托管文件。
   除 `.well-known path` 名称外，URL 中不能有其他路径。

1. 当 `did-configuration.json` 在 .well-known/did-configuration.json URL 下公开可用时，请按“**刷新验证状态**”按钮进行验证。

   ![图像](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/49a06251-af56-49b2-9059-0bd4ca678da6)

> 结果：已成功创建 Azure Key Vault 实例、设置已验证 ID 服务、在 Azure AD 中注册应用程序，以及分散式标识符（DID）的已验证域所有权。

**清理资源**

> 记得删除所有不再使用的新建 Azure 资源。 删除未使用的资源，确保不产生意外成本。 
