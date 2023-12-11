---
lab:
  title: 13 - 部署 Azure VM 和 Log Analytics 工作区
  module: Module 04 - Manage security operations
---

# 实验室 13：部署 Azure VM 和 Log Analytics 工作区

# 学生实验室手册

## 实验室方案

系统要求你创建 Azure 虚拟机和 Log Analytics 工作区。

- 部署 Azure 虚拟机。
- 创建 Log Analytics 工作区。

> 对于本实验室中的所有资源，我们使用“美国东部”区域。 请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：创建 Azure 虚拟机
  
## 说明

### 练习 1：部署 Azure 虚拟机

### 练习计时：10 分钟

在本练习中，你将完成以下任务： 

- 任务 1：部署 Azure 虚拟机 

#### 任务 1：部署 Azure 虚拟机

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

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
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

#### 任务 2：创建 Log Analytics 工作区

此任务将创建一个 Log Analytics 工作区。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Log Analytics 工作区”，然后按“Enter”键。

2. 在“Log Analytics 工作区”边栏选项卡上，单击“+ 创建” **** 。

3. 在“创建 Log Analytics 工作区”边栏选项卡的“基本设置”选项卡上，指定以下设置（其他设置保留默认值） ：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB131415|
    |名称|任何有效的全局唯一名称|
    |区域|**美国东部**|

4. 选择“查看 + 创建”  。

5. 在“创建 Log Analytics 工作区”边栏选项卡的“查看 + 创建”选项卡中，选择“创建”  。

> 结果：你已部署 Azure 虚拟机并创建了 Log Analytics 工作区。

>注意：请勿移除此实验室中的资源，因为 Microsoft Defender for Cloud 实验室和 Microsoft Sentinel 实验室需要这些资源。
 
