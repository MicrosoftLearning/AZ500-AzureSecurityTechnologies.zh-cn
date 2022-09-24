---
lab:
  title: 07 - 网络安全组和应用程序安全组
  module: Module 02 - Implement Platform Protection
---

# <a name="lab-07-network-security-groups-and-application-security-groups"></a>实验室 07：网络安全组和应用程序安全组
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to implement your organization's virtual networking infrastructure and test to ensure it is working correctly. In particular:

- 该组织有两组服务器：Web 服务器和管理服务器。
- 每组服务器应位于其自己的应用程序安全组中。 
- 你应该能够 RDP 进入管理服务器，但不是进入 Web 服务器。
- 从 Internet 访问时，Web 服务器应显示 IIS 网页。 
- 应使用网络安全组规则来控制网络访问。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：创建虚拟网络基础结构
- 练习 2：部署虚拟机并测试网络筛选器

## <a name="network-and-application-security-groups-diagram"></a>网络和应用程序安全组示意图

![image](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## <a name="instructions"></a>说明

### <a name="exercise-1-create-the-virtual-networking-infrastructure"></a>练习 1：创建虚拟网络基础结构

### <a name="estimated-timing-20-minutes"></a>预计用时：20 分钟

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East (US)<ept id="p1">**</ept> region. Verify with your instructor this is region to use for you class. 

在本练习中，你将完成以下任务：

- 任务 1：创建带有一个子网的虚拟网络。
- 任务 2：创建两个应用程序安全组。
- 任务 3：创建一个网络安全组，并将其与虚拟网络子网关联。
- 任务 4：为所有到 Web 服务器的流量和到管理服务器的 RDP 创建入站 NSG 安全规则。

#### <a name="task-1--create-a-virtual-network"></a>任务 1：创建虚拟网络

在此任务中，将创建一个虚拟网络，以便与网络和应用程序安全组一起使用。 

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“虚拟网络”，然后按 Enter 键  。

3. 在“虚拟网络”边栏选项卡上，单击“+ 创建” 。

4. 在“创建虚拟网络”边栏选项卡的“基本信息”选项卡上，指定以下设置（其他设置保留默认值），然后单击“下一步:   IP 地址”：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|单击“新建”并键入名称“AZ500LAB07”|
    |名称|**myVirtualNetwork**|
    |区域|**美国东部**|

5. 在“创建虚拟网络”边栏选项卡的“IP 地址”选项卡中，将“IPv4 地址空间”设置为“10.0.0.0/16”，如有必要，在“子网名称”列中单击“默认”，在“编辑子网”边栏选项卡中指定以下设置并单击“保存”：

    |设置|值|
    |---|---|
    |子网名称|**default**|
    |子网地址范围|**10.0.0.0/24**|

6. 返回“创建虚拟网络”边栏选项卡的“IP 地址”选项卡，单击“查看 + 创建”。

7. 在“创建虚拟网络”边栏选项卡的“查看 + 创建”选项卡中，单击“创建”。

#### <a name="task-2--create-application-security-groups"></a>任务 2：创建应用程序安全组

在此任务中，将创建一个应用程序安全组。

1. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“应用程序安全组”，然后按 Enter 键。

2. 在“应用程序安全组”边栏选项卡中，单击“+ 创建”。

3. 在“创建应用程序安全组”边栏选项卡的“基本信息”选项卡中，指定以下设置： 

    |设置|值|
    |---|---|
    |资源组|AZ500LAB07|
    |名称|**myAsgWebServers**|
    |区域|**美国东部**|

    >**注意**：该组将用于 Web 服务器。

4. 依次单击“查看 + 创建”、“创建”。 

5. 导航回“应用程序安全组”边栏选项卡，并单击“+ 创建”。

6. 在“创建应用程序安全组”边栏选项卡的“基本信息”选项卡中，指定以下设置： 

    |设置|值|
    |---|---|
    |资源组|AZ500LAB07|
    |名称|**myAsgMgmtServers**|
    |区域|**美国东部**|

    >**注意**：该组将用于管理服务器。

7. 依次单击“查看 + 创建”、“创建”。 

#### <a name="task-3--create-a-network-security-group-and-associate-the-nsg-to-the-subnet"></a>任务 3：创建网络安全组并将 NSG 关联到子网

在此任务中，将创建一个网络安全组。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“网络安全组”，然后按 Enter 键  。

2. 在“网络安全组”边栏选项卡中，单击“+ 创建”。

3. 在“创建网络安全组”边栏选项卡的“基本”选项卡中，指定以下设置： 

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB07|
    |名称|**myNsg**|
    |区域|**美国东部**|

4. 依次单击“查看 + 创建”、“创建”。 

5. 在 Azure 门户中，导航回“网络安全组”边栏选项卡，并单击“myNsg”条目。

6. 在“myNsg”边栏选项卡中，在“设置”部分单击“子网”，然后单击“+ 关联”。 

7. 在“关联子网”边栏选项卡中，指定以下设置，并单击“确定”：

    |设置|值|
    |---|---|
    |虚拟网络|**myVirtualNetwork**|
    |子网|**default**|

#### <a name="task-4-create-inbound-nsg-security-rules-to-all-traffic-to-web-servers-and-rdp-to-the-management-servers"></a>任务 4：为所有到 Web 服务器的流量和到管理服务器的 RDP 创建入站 NSG 安全规则。 

1. 在“myNsg”边栏选项卡中的“设置”部分，单击“入站安全规则”。

2. 查看默认入站安全规则，然后单击“+ 添加”。

3. 在“添加入站安全规则”边栏选项卡中，指定以下设置，以允许 TCP 端口 80 和 443 连接到“myAsgWebServers”应用程序安全组（将所有其他值保留为默认值）： 

    |设置|Value|
    |---|---|
    |目标|在下拉列表中，选择“应用程序安全组”，然后单击“myAsgWebServers”|
    |目标端口范围|**80,443**|
    |协议|**TCP**|
    |优先级|**100**|                                                    
    |名称|**Allow-Web-All**|

4. 在“添加入站安全规则”边栏选项卡上，单击“添加”以创建新的入站规则 。 

5. 在“myNsg”边栏选项卡上的“设置”部分，单击“入站安全规则”，然后单击“+ 添加”。

6. 在“添加入站安全规则”边栏选项卡上，指定以下设置以允许 RDP 端口 (TCP 3389) 连接到“myAsgMgmtServers”应用程序安全组（将所有其他值保留为默认值）： 

    |设置|Value|
    |---|---|
    |目标|在下拉列表中，选择“应用程序安全组”，然后单击“myAsgMgmtServers”|
    |目标端口范围|**3389**|
    |协议|**TCP**|
    |优先级|**110**|                                                    
    |名称|**Allow-RDP-All**|

7. 在“添加入站安全规则”边栏选项卡上，单击“添加”以创建新的入站规则 。 

> 结果：你已经部署了一个虚拟网络、具有入站安全规则的网络安全和两个应用程序安全组。 

### <a name="exercise-2-deploy-virtual-machines-and-test-network-filters"></a>练习 2：部署虚拟机并测试网络筛选器

### <a name="estimated-timing-25-minutes"></a>预计用时：25 分钟

在本练习中，你将完成以下任务：

- 任务 1：创建一个用作 Web 服务器的虚拟机。
- 任务 2：创建一个用作管理服务器的虚拟机。 
- 任务 3：将每个虚拟机网络接口与其应用程序安全组相关联。
- 任务 4：测试网络流量筛选。

#### <a name="task-1-create-a-virtual-machine-to-use-as-a-web-server"></a>任务 1：创建一个用作 Web 服务器的虚拟机。

在此任务中，将创建一个用作 Web 服务器的虚拟机。

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键  。

2. 在“虚拟机”边栏选项卡上，单击“+ 创建”，然后在下拉列表中单击“+ Azure 虚拟机”  。

3. 在“创建虚拟机”边栏选项卡的“基本信息”选项卡上，指定以下设置（将其他设置保留为默认值） ：

   |设置|值|
   |---|---|
   |订阅|将在此实验室中使用的 Azure 订阅的名称|
   |资源组|AZ500LAB07|
   |虚拟机名称|**myVmWeb**|
   |区域|（美国）美国东部|
   |映像|**Windows Server 2022 Datacenter：Azure 版本 - Gen2**|
   |大小|Standard D2s v3|
   |用户名|**学生**|
   |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
   |确认密码|重新键入密码|
   |公共入站端口|**无**|
   |是否要使用现有的 Windows Server 许可证 |**否**|

    >**注意**：对于公共入站端口，我们将依赖于预先创建的 NSG。 

4. 单击“下一步:磁盘 >”，在“创建虚拟机”边栏选项卡的“磁盘”选项卡上，将“OS 磁盘类型”设置为“标准 HDD”，然后单击“下一步:网络 >”。

5. 在“创建虚拟机”边栏选项卡的“网络”选项卡上，选择之前创建的网络“myVirtualNetwork”。

6. 在“NIC 网络安全组”下，选择“无”。

7. 单击“下一步:管理 >”，在“创建虚拟机”边栏选项卡的“管理”选项卡上，验证以下设置：

   |设置|值|
   |---|---|
   |启动诊断|已使用托管存储帐户启用（推荐）|

8. 单击“查看 + 创建”，在“查看 + 创建”边栏选项卡上，确保验证成功，然后单击“创建”。

#### <a name="task-2-create-a-virtual-machine-to-use-as-a-management-server"></a>任务 2：创建一个用作管理服务器的虚拟机。 

在此任务中，将创建用作管理服务器的虚拟机。

1. 在 Azure 门户中，导航回“虚拟机”边栏选项卡上，单击“+ 创建”，然后在下拉列表中单击“+ Azure 虚拟机”  。

2. 在“创建虚拟机”边栏选项卡的“基本信息”选项卡上，指定以下设置（将其他设置保留为默认值） ：

   |设置|值|
   |---|---|
   |订阅|将在此实验室中使用的 Azure 订阅的名称|
   |资源组|AZ500LAB07|
   |虚拟机名称|myVMMgmt|
   |区域|（美国）美国东部|
   |映像|**Windows Server 2022 Datacenter：Azure 版本 - Gen 2**|
   |大小|Standard D2s v3|
   |用户名|**学生**|
   |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
   |公共入站端口|**无**|
   |已拥有 Windows Server 许可证|**否**|

    >**注意**：对于公共入站端口，我们将依赖于预先创建的 NSG。 

3. 单击“下一步:磁盘 >”，在“创建虚拟机”边栏选项卡的“磁盘”选项卡上，将“OS 磁盘类型”设置为“标准 HDD”，然后单击“下一步:网络 >”。

4. 在“创建虚拟机”边栏选项卡的“网络”选项卡上，选择之前创建的网络“myVirtualNetwork”。

5. 在“NIC 网络安全组”下，选择“无”。

6. 单击“下一步:管理 >”，在“创建虚拟机”边栏选项卡的“管理”选项卡上，指定以下设置

   |设置|值|
   |---|---|
   |启动诊断|已使用托管存储帐户启用（推荐）|

7. 单击“查看 + 创建”，在“查看 + 创建”边栏选项卡上，确保验证成功，然后单击“创建”。

    >**注意**：等待两个虚拟机预配完成，然后再继续。 

#### <a name="task-3-associate-each-virtual-machines-network-interface-to-its-application-security-group"></a>任务 3：将每个虚拟机网络接口与其应用程序安全组相关联。

In this task, you will associate each virtual machines network interface with the corresponding application security group. The myVMWeb virtual machine interface will be associated to the myAsgWebServers ASG. The myVMMgmt virtual machine interface will be associated to the myAsgMgmtServers ASG. 

1. 在 Azure 门户中，导航回“虚拟机”边栏选项卡，并验证两个虚拟机是否都已列出并显示“正在运行”状态。

2. 在虚拟机列表中，单击“myVMWeb”条目。

3. 在“myVMWeb”边栏选项卡的“设置”部分，单击“网络”，然后在“myVMWeb \| 网络”边栏选项卡上，单击“应用程序安全组”选项卡。

4. 单击“配置应用程序安全组”，在“应用程序安全组”下拉列表中选择“myAsgWebServers”，然后单击“保存”。

5. 导航回“虚拟机”边栏选项卡，然后在虚拟机列表中单击“myVMMgmt”条目。

6. 在“myVMMgmt”边栏选项卡的“设置”部分，单击“网络”，然后在“myVMMgmt \| 网络”边栏选项卡上，单击“应用程序安全组”选项卡。

7. 单击“配置应用程序安全组”，在“应用程序安全组”下拉列表中选择“myAsgMgmtServers”，然后单击“保存”。

#### <a name="task-4-test-the-network-traffic-filtering"></a>任务 4：测试网络流量筛选

In this task, you will test the network traffic filters. You should be able to RDP into the myVMMgmnt virtual machine. You should be able to connect from the internet to the myVMWeb virtual machine and view the default IIS web page.  

1. 导航回“myVMMgmt”虚拟机边栏选项卡。

2. 在“myVMMgmt”边栏选项卡上单击“连接”，然后在下拉菜单中单击“RDP”。 

3. 你被要求实现组织的虚拟网络基础结构，并进行测试以确保其正常运行。

   |设置|值|
   |---|---|
   |用户名|**学生**|
   |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >具体而言：

4. 在 Azure 门户中，导航到“myVMWeb”虚拟机边栏选项卡。

5. 在“myVMWeb”边栏选项卡的“操作”部分，单击“运行命令”，然后单击“RunPowerShellScript”。

6. 在“运行命令脚本”窗格中运行以下命令，将 Web 服务器角色安装到“myVmWeb”上：

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the installation to complete. This might take a couple of minutes. At that point, you can verify that myVMWeb can be accessed via HTTP/HTTPS.

7. 在 Azure 门户中，导航回“myVMWeb”边栏选项卡。

8. 在“myVMWeb”边栏选项卡上，确定 myVmWeb Azure VM 的“公共 IP 地址”。

9. 打开另一个浏览器选项卡，然后导航到上一步中确定的 IP 地址。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The browser page should display the default IIS welcome page because port 80 is allowed inbound from the internet based on the setting of the <bpt id="p2">**</bpt>myAsgWebServers<ept id="p2">**</ept> application security group. The network interface of the myVMWeb Azure VM is associated with that application security group. 

> 结果：你已验证 NSG 和 ASG 配置正在生效，并且流量正在得到正确管理。 

**清理资源**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. 

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  关闭 Cloud Shell 窗格。 
