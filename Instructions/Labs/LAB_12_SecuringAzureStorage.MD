---
lab:
  title: ' 12 - 服务终结点和安全存储'
  module: Module 03 - Secure data and applications
---

# <a name="lab-12-service-endpoints-and-securing-storage"></a>实验室 12：服务终结点和保护存储
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to create a proof of concept to demonstrate securing Azure file shares. Specifically, you want to:
    
- 创建一个存储终结点，以便发往 Azure 存储的流量始终留在 Azure 主干网络内。
- 配置该存储终结点，以便仅来自特定子网的资源可以访问存储。
- 确认特定子网之外的资源无法访问存储。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：服务终结点和安全存储

## <a name="service-endpoints-and-securing-storage-diagram"></a>服务终结点和安全存储示意图

![image](https://user-images.githubusercontent.com/91347931/157534883-29664a05-85d1-4c70-99a7-f16d2360755d.png)

## <a name="instructions"></a>说明

### <a name="exercise-1-service-endpoints-and-security-storage"></a>练习 1：服务终结点和安全存储

### <a name="estimated-timing-45-minutes"></a>预计用时：45 分钟

在本练习中，你将完成以下任务：

- 任务 1：创建虚拟网络
- 任务 2：将子网添加到虚拟网络并配置存储终结点
- 任务 3：配置网络安全组以限制对子网的访问
- 任务 4：配置网络安全组以支持公共子网上的 rdp
- 任务 5：使用文件共享创建存储帐户
- 任务 6：将虚拟机部署到指定的子网中
- 任务 7：测试来自专用子网的存储连接，以确认允许访问
- 任务 8：测试来自公共子网的存储连接，以确认访问被拒绝 

#### <a name="task-1-create-a-virtual-network"></a>任务 1：创建虚拟网络

在此任务中，你将创建一个虚拟网络。

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

2. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中键入“虚拟网络”，然后按 Enter 键  。

3. 在“虚拟网络”边栏选项卡上，单击“+ 创建” **** 。

4. 在“创建虚拟网络”边栏选项卡的“基本信息”选项卡上，指定以下设置（其他设置保留默认值），然后单击“下一步:   IP 地址”：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|单击“新建”并键入名称 AZ500LAB12|
    |名称|**myVirtualNetwork**|
    |区域|**（美国）美国东部**|

5. 在“创建虚拟网络”边栏选项卡的“IP 地址”选项卡上，将“IPv4 地址空间”设置为“10.0.0.0/16”，在“子网名称”列，单击“默认”，在“编辑子网”边栏选项卡上，指定以下设置并单击“保存”：

    |设置|值|
    |---|---|
    |子网名称|**公共**|
    |子网地址范围|**10.0.0.0/24**|

6. 返回“创建虚拟网络”边栏选项卡的“IP 地址”选项卡，单击“查看 + 创建”。

7. 在“创建虚拟网络”边栏选项卡的“查看 + 创建”选项卡中，单击“创建”。

#### <a name="task-2-add-a-subnet-to-the-virtual-network-and-configure-a-storage-endpoint"></a>任务 2：将子网添加到虚拟网络并配置存储终结点

In this task, you will create another subnet and enable a service endpoint on that subnet. Service endpoints are enabled per service, per subnet. 

1. 在 Azure 门户中，导航回“虚拟网络”边栏选项卡。

2. 在“虚拟网络”边栏选项卡中，单击“myVirtualNetwork”条目。

3. 在“myVirtualNetwork”边栏选项卡上的“设置”部分，单击“子网”。

4. 在“myVirtualNetwork \| 子网”边栏选项卡上，单击“+ 子网” 。 

5. 在“添加子网”边栏选项卡上，请指定以下设置（将其他设置保留为默认值）：

    |设置|值|
    |---|---|
    |子网名称|**专用**|
    |子网地址范围|**10.0.1.0/24**|
    |服务终结点|**Microsoft.Storage**|

6. 在“添加子网”边栏选项卡上单击“保存”以添加新的子网 。

    >**注意**：虚拟网络现在有两个子网：公用和专用。 
    
#### <a name="task-3-configure-a-network-security-group-to-restrict-access-to-the-subnet"></a>任务 3：配置网络安全组以限制对子网的访问

In this task, you will create a network security group with two outbound security rules (Storage and internet) and one inbound security rule (RDP). You will also associate the network security group with the Private subnet. This will restrict outbound traffic from Azure VMs connected to that subnet.

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“网络安全组”，然后按 Enter 键  。

2. 在“网络安全组”边栏选项卡中，单击“+ 创建”。

3. 在“创建网络安全组”边栏选项卡的“基本”选项卡中，指定以下设置： 

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB12|
    |名称|**myNsgPrivate**|
    |区域|**美国东部**|

4. 依次单击“查看 + 创建”、“创建”。 

    >**注意**：在接下来的步骤中，你将创建一个允许与 Azure 存储服务进行通信的出站安全规则。 

5. 在 Azure 门户中，导航到“网络安全组”边栏选项卡，并单击“myNsgPrivate”条目。

6. 在“myNsgPrivate”边栏选项卡上，在“设置”部分，单击“出站安全规则”。

7. 在“myNsgPrivate \| 出站安全规则”边栏选项卡上，单击“+ 添加”。

8. 在“添加出站安全规则”边栏选项卡上，请指定以下设置以显式允许到 Azure 存储的出站流量（将所有其他值保留为其默认设置）： 

    |设置|值|
    |---|---|
    |源|**服务标记**|
    |源服务标记|**VirtualNetwork**|
    |源端口范围|**\***|
    |目标|**服务标记**|
    |目标服务标记|**存储**|
    |目标端口范围|**\***|
    |协议|**任意**|
    |操作|**允许**|
    |优先级|**1000**|
    |名称|**Allow-Storage-All**|

9. 在“添加出站安全规则”边栏选项卡上，单击“添加”以创建新的出站规则 。 

10. 在“myNsgPrivate”边栏选项卡上的“设置”部分，单击“出站安全规则”，然后单击“+添加”。

11. 在“添加出站安全规则”边栏选项卡上，指定以下设置以显式拒绝到 Internet 的出站流量（所有其他值保留为默认设置）： 

    |设置|值|
    |---|---|
    |源|**服务标记**|
    |源服务标记|**VirtualNetwork**|
    |源端口范围|**\***|
    |目标|**服务标记**|
    |目标服务标记|**Internet**|
    |目标端口范围|**\***|
    |协议|**任意**|
    |操作|**拒绝**|
    |优先级|1100|
    |名称|**Deny-Internet-All**|

    >**注意**：此规则将替代所有网络安全组中允许出站 Internet 通信的默认规则。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In the next steps, you will create an inbound security rule that allows Remote Desktop Protocol (RDP) traffic to the subnet. The rule overrides a default security rule that denies all inbound traffic from the internet. Remote Desktop connections are allowed to the subnet so that connectivity can be tested in a later step.

12. 在“myNsgPrivate”边栏选项卡上的“设置”部分，单击“入站安全规则”，然后单击“+添加”。

13. 在“添加入站安全规则”边栏选项卡上，请指定以下设置（将所有其他值保留为其默认值）： 

    |设置|值|
    |---|---|
    |源|**任意**|
    |源端口范围|**\***|
    |目标|**服务标记**|
    |目标服务标记|**VirtualNetwork**|
    |目标端口范围|**3389**|
    |协议|**TCP**|
    |操作|**允许**|
    |优先级|**1200**|                                                    
    |名称|**Allow-RDP-All**|

14. 在“添加入站安全规则”边栏选项卡上，单击“添加”以创建新的入站规则 。 

    >**注意**：现在，你将网络安全组与专用子网相关联。

15. 在“子网”边栏选项卡中，选择“+ 关联”并在“关联子网”部分指定以下设置，然后单击“确定”：

    |设置|值|
    |---|---|
    |虚拟网络|**myVirtualNetwork**|
    |子网|**专用**|

#### <a name="task-4-configure-a-network-security-group-to-allow-rdp-on-the-public-subnet"></a>任务 4：配置网络安全组以支持公共子网上的 rdp

你需要创建一个概念证明来演示如何保护 Azure 文件共享。

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“网络安全组”，然后按 Enter 键  。

2. 在“网络安全组”边栏选项卡中，单击“+ 创建”。

3. 在“创建网络安全组”边栏选项卡的“基本”选项卡中，指定以下设置： 

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB12|
    |名称|myNsgPublic|
    |区域|**美国东部**|

4. 依次单击“查看 + 创建”、“创建”。 

    >**注意**：在接下来的步骤中，你将创建一个允许与 Azure 存储服务进行通信的出站安全规则。 

5. 在 Azure 门户中，导航回“网络安全组”边栏选项卡，并单击“myNsgPublic”条目 。

6. 在“myNsgPublic”边栏选项卡上的“设置”部分，单击“入站安全规则”，然后单击“+ 添加”   。

7. 在“添加入站安全规则”边栏选项卡上，请指定以下设置（将所有其他值保留为其默认值）： 

    |设置|值|
    |---|---|
    |源|**任意**|
    |源端口范围|**\***|
    |目标|**服务标记**|
    |目标服务标记|**VirtualNetwork**|
    |目标端口范围|**3389**|
    |协议|**TCP**|
    |操作|**允许**|
    |优先级|**1200**|                                                    
    |名称|**Allow-RDP-All**|

8. 在“添加入站安全规则”边栏选项卡上，单击“添加”以创建新的入站规则 。 

    >注意：现在要将网络安全组与专用子网关联。

9. 在“子网”边栏选项卡中，选择“+ 关联”并在“关联子网”部分指定以下设置，然后单击“确定”：

    |设置|值|
    |---|---|
    |虚拟网络|**myVirtualNetwork**|
    |子网|**公共**|
    
#### <a name="task-5-create-a-storage-account-with-a-file-share"></a>任务 5：使用文件共享创建存储帐户

在此任务中，你将创建一个具有文件共享的存储帐户并获取存储帐户密钥。  

1. 在 Azure 门户中，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“存储帐户”，然后按 Enter 键。

2. 在“存储帐户”边栏选项卡上，单击“+ 创建” **** 。

3. 在“创建存储帐户”边栏选项卡的“基本信息”选项卡上，指定以下设置（其他设置保留默认值） ：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB12|
    |存储帐户名称|由字母和数字组成、长度介于 3 到 24 个字符之间的任意全局唯一名称|
    |位置|（美国）美国东部|
    |性能|标准（常规用途 v2 帐户）|
    |冗余|**本地冗余存储 (LRS)**|

4. 在“创建存储帐户”边栏选项卡的“基本信息”选项卡中，单击“查看 + 创建”，等待验证过程完成，然后单击“创建”。

    >具体来说，你需要：

5. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“资源组”，然后按 Enter 键  。

6. 在“资源组”边栏选项卡的资源组列表中，单击“AZ500LAB12”条目。

7. 在“AZ500LAB12”资源组边栏选项卡的资源列表中，单击表示新创建的存储账户的条目。

8. 在存储帐户的“概述”边栏选项卡上，单击“数据存储”选项卡下的“文件共享”，然后单击“+ 文件共享”   。

9. 在“新建文件共享”边栏选项卡上，指定以下设置：

    |设置|值|
    |---|---|
    |名称|**my-file-share**|

10. 在“新建文件共享”边栏选项卡上，单击“创建”。

    >**注意**：现在，检索并记录用于创建到 Azure 文件共享的驱动器映射的 PowerShell 脚本。 

11. 在“存储帐户”边栏选项卡上的文件共享列表中，单击“my-file-share”。

12. 在“my-file-share”边栏选项卡上，单击“连接”。

13. 在“连接”边栏选项卡上，在“窗口”选项卡中，将创建 Z 驱动器映射的 PowerShell 脚本复制到文件共享。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this script. You will need this in a later in this lab in order to map the file share from the Azure virtual machine on the <bpt id="p1">**</bpt>Private<ept id="p1">**</ept> subnet.

14. 导航回“存储帐户”边栏选项卡，然后在“安全 + 网络”部分，单击“网络”。

15. 在“防火墙和虚拟网”边栏选项卡下，选择“已从所选虚拟网络和 IP 地址启用”选项并单击“+ 添加现有虚拟网络”链接  。 

16. 在“添加网络”边栏选项卡上，指定以下设置：

    |设置|值|
    |---|---|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |虚拟网络|**myVirtualNetwork**|
    |子网|**专用**|

17. 在“添加网络”边栏选项卡上，单击“添加” 。 

18. 返回到“存储帐户”边栏选项卡，单击“保存”。

    >**注意**：至此，你已在实验室中配置虚拟网络、网络安全组和具有文件共享的存储帐户。 

#### <a name="task-6-deploy-virtual-machines-into-the-designated-subnets"></a>任务 6：将虚拟机部署到指定的子网中

在此任务中，你将创建两个虚拟机，一个在专用子网中，一个在公共子网中。 

>**注意**：第一个虚拟机将连接到专用子网。

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“虚拟机”，然后按 Enter 键  。

2. 在“虚拟机”边栏选项卡上，单击“+ 创建”，然后在下拉列表中单击“+ Azure 虚拟机”  

3. 在“创建虚拟机”边栏选项卡的“基本信息”选项卡上，指定以下设置（将其他设置保留为默认值） ：

    |设置|值|
    |---|---|
    |订阅|将在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB12|
    |虚拟机名称|**myVmPrivate**|
    |区域|（美国）美国东部|
    |映像|Windows Server 2022 Datacenter：Azure 版本 - Gen 2|
    |用户名|localadmin|
    |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
    |公共入站端口|**无**|
    |已拥有 Windows Server 许可证|**否**|

    >**注意**：对于公共入站端口，我们将依赖于预先创建的 NSG。 

4. 单击“下一步:磁盘 >”，在“创建虚拟机”边栏选项卡的“磁盘”选项卡上，将“OS 磁盘类型”设置为“标准 HDD”，然后单击“下一步:网络 >”。

5. 单击“下一步:网络 >”，在“创建虚拟机”边栏选项卡的“网络”选项卡上，指定以下设置（其他设置保留为默认值）：

    |设置|值|
    |---|---|
    |虚拟网络|**myVirtualNetwork**|
    |子网|“专用(10.0.1.0/24)”|
    |公共 IP|(new)myVmPrivate-ip|
    |NIC 网络安全组|**无**|

6. 单击“下一步:管理 >”，在“创建虚拟机”边栏选项卡的“管理”选项卡中，接受默认设置，然后单击“查看 + 创建”。

7. 在“查看 + 创建”边栏选项卡上，请确保验证成功，然后单击“创建” 。

    >**注意**：第二个虚拟机将连接到公共子网。

8. 在“虚拟机”边栏选项卡上，单击“+ 添加”，然后在下拉列表中单击“+ Azure 虚拟机”。  

9. 在“创建虚拟机”边栏选项卡的“基本信息”选项卡上，指定以下设置（将其他设置保留为默认值） ：

    |设置|值|
    |---|---|
    |订阅|将在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB12|
    |虚拟机名称|**myVmPublic**|
    |区域|（美国）美国东部|
    |映像|Windows Server 2022 Datacenter：Azure 版本 - Gen 2|
    |用户名|localadmin|
    |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|
    |公共入站端口|**无**|
    |已拥有 Windows Server 许可证|**否**|

    >**注意**：对于公共入站端口，我们将依赖于预先创建的 NSG。 

10. 单击“下一步:磁盘 >”，在“创建虚拟机”边栏选项卡的“磁盘”选项卡上，将“OS 磁盘类型”设置为“标准 HDD”，然后单击“下一步:网络 >”。

11. 单击“下一步:网络 >”，在“创建虚拟机”边栏选项卡的“网络”选项卡上，指定以下设置（其他设置保留为默认值）：

    |设置|值|
    |---|---|
    |虚拟网络|**myVirtualNetwork**|
    |子网|“公共(10.0.0.0/24)”|
    |公共 IP|(new)myVmPublic-ip|
    |NIC 网络安全组|**无**|

12. 单击“下一步:管理 >”，在“创建虚拟机”边栏选项卡的“管理”选项卡中，接受默认设置，然后单击“查看 + 创建”。

13. 在“查看 + 创建”边栏选项卡上，请确保验证成功，然后单击“创建” 。

    >**注意**：myVMPrivate Azure VM 部署完成后就可以继续下一个任务。

#### <a name="task-7-test-the-storage-connection-from-the-private-subnet-to-confirm-that-access-is-allowed"></a>任务 7：测试来自专用子网的存储连接，以确认允许访问

在此任务中，你将通过远程桌面连接到 myVMPrivate 虚拟机，并将驱动器映射到文件共享。 

1. 导航回“虚拟机”边栏选项卡。 

2. 在“虚拟机”边栏选项卡上，单击“myVMPrivate”条目。

3. 在“myVMPrivate”边栏选项卡上，单击“连接”，然后在下拉菜单中，单击“RDP”。 

4. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>myVMPrivate<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

    |设置|值|
    |---|---|
    |用户名|localadmin|
    |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >**注意**：等待打开远程桌面会话并加载服务器管理器。

    >**注意**：现在，你将驱动器 Z 映射到 Windows Server 2022 计算机的远程桌面会话中的 Azure 文件共享

5. 在与 myVMPrivate 的远程桌面会话中，单击“启动”，然后单击“Windows PowerShell ISE”。

6. Within the <bpt id="p1">**</bpt>Windows PowerShell ISE<ept id="p1">**</ept> window, open the <bpt id="p2">**</bpt>Script<ept id="p2">**</ept> pane, then paste and run the PowerShell script that you recorded earlier in this lab. The script has the following format:

    ```powershell
    $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
    if ($connectTestResult.TcpTestSucceeded) {
       # Save the password so the drive will persist on reboot
       cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
       # Mount the drive
       New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
    } else {
       Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
    }
    ```
    >**注意**：占位符 `<storage-account-name>` 代表托管文件共享的存储帐户的名称，而 `<storage_account_name>` 代表它的主键

7. 启动“文件资源管理器”，并验证是否已成功创建 Z: 驱动器映射。

8. 接下来，在 Windows PowerShell ISE 控制台的“控制台”窗格中，运行以下命令以验证虚拟机没有到 Internet 的出站连接：

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**注意**：测试将失败，因为与专用子网关联的网络安全组不允许到 Internet 的出站访问。

9. 终止与 myVMPrivate Azure VM 的远程桌面会话。

    >**注意**：至此，你已确认专用子网中的虚拟机可以访问存储帐户。 

####  <a name="task-8-test-the-storage-connection-from-the-public-subnet-to-confirm-that-access-is-denied"></a>任务 8：测试来自公共子网的存储连接，以确认访问被拒绝 

1. 导航回“虚拟机”边栏选项卡。 

2. 在“虚拟机”边栏选项卡上，单击“myVMPublic”条目。

3. 在“myVMPublic”边栏选项卡上，单击“连接”，然后在下拉菜单中，单击“RDP”。 

4. Click <bpt id="p1">**</bpt>Download RDP File<ept id="p1">**</ept> and use it to connect to the <bpt id="p2">**</bpt>myVMPublic<ept id="p2">**</ept> Azure VM via Remote Desktop. When prompted to authenticate, provide the following credntials:

    |设置|值|
    |---|---|
    |用户名|localadmin|
    |密码|请使用在实验室 04 > 练习 1 > 任务 1 > 步骤 9 中创建的个人密码。|

    >**注意**：等待打开远程桌面会话并加载服务器管理器。

    >**注意**：现在，你将驱动器 Z 映射到 Windows Server 2022 计算机的远程桌面会话中的 Azure 文件共享

5. 在与 myVMPublic  的远程桌面会话中，单击“启动”，然后单击“Windows PowerShell ISE”。

6. 在 Windows PowerShell ISE 窗口，打开“脚本”窗格，然后远程桌面会话中运行的同一 PowerShell 脚本粘贴到 myVMPrivate Azure VM 并运行该脚本。

    >**注意**：这次会收到“New-PSDrive: 访问被拒绝”错误。 

    >对于本实验室中的所有资源，我们使用“美国东部”区域。

7. 接下来，在 Windows PowerShell ISE 控制台的“控制台”窗格中运行以下命令，以确认虚拟机有到 Internet 的出站连接： 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >注意：测试将成功，因为公共子网上没有拒绝 Internet 的出站安全规则。

8. 终止与 myVMPublic Azure VM 的远程桌面会话。

    >**注意**：至此，你已确认公共子网中的虚拟机无法访问存储帐户，但是可以访问 Internet。


**清理资源**

> 请与讲师确认这是课堂上所使用的区域。 

1. Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select <bpt id="p1">**</bpt>PowerShell<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

2. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中“PowerShell”。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  关闭 Cloud Shell 窗格。 

