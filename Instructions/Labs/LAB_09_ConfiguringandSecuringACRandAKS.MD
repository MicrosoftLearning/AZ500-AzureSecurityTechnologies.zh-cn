---
lab:
  title: 09 - 配置和保护 ACR 和 AKS
  module: Module 02 - Implement Platform Protection
---

# <a name="lab-09-configuring-and-securing-acr-and-aks"></a>实验室 09：配置和保护 ACR 和 AKS
# <a name="student-lab-manual"></a>学生实验室手册

## <a name="lab-scenario"></a>实验室方案

You have been asked to deploy a proof of concept with Azure Container Registry and Azure Kubernetes Service. Specifically, the proof of concept should demonstrate:

- 使用 Dockerfile 生成映像。
- 使用 Azure 容器注册表存储映像。
- 配置 Azure Kubernetes 服务。
- 在内部和外部保护和访问容器应用程序。 

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East US<ept id="p1">**</ept> region. Verify with your instructor this is the region to use for class. 

## <a name="lab-objectives"></a>实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：配置和保护 ACR 和 AKS

## <a name="configuring-and-securing-acr-and-aks-diagram"></a>配置和保护 ACR 和 AKS 的示意图

![image](https://user-images.githubusercontent.com/91347931/157532250-1104a829-792a-4b6d-beff-fe976e2d5d4b.png)

## <a name="intructions"></a>说明

## <a name="lab-files"></a>实验室文件：

- \\Allfiles\\Labs\\09\\nginxexternal.yaml
- \\Allfiles\\Labs\\09\\nginxinternal.yaml

### <a name="exercise-1-configuring-and-securing-acr-and-aks"></a>练习 1：配置和保护 ACR 和 AKS

### <a name="estimated-timing-45-minutes"></a>预计用时：45 分钟

> For all the resources in this lab, we are using the <bpt id="p1">**</bpt>East (US)<ept id="p1">**</ept> region. Verify with your instructor this is region to use for you class. 

在本练习中，你将完成以下任务：

- 任务 1：创建 Azure 容器注册表
- 任务 2：创建 Dockerfile、构建容器并将其推送到 Azure 容器注册表
- 任务 3：创建 Azure Kubernetes 服务群集
- 任务 4：授予 AKS 群集访问 ACR 的权限
- 任务 5：将外部服务部署到 AKS
- 任务 6：验证你是否可以访问外部 AKS 托管的服务
- 任务 7：将内部服务部署到 AKS
- 任务 8：验证你是否可以访问 AKS 托管的内部服务

#### <a name="task-1-create-an-azure-container-registry"></a>任务 1：创建 Azure 容器注册表

在此任务中，你将为实验室创建资源组和 Azure 容器注册表。

1. 登录到 Azure 门户 `https://portal.azure.com/`。

    >**注意**：使用帐户登录到 Azure 门户，该帐户在你用于此实验室的 Azure 订阅中具有所有者或参与者角色，并且在与该订阅关联的 Azure AD 租户中具有全局管理员角色。

2. In the Azure portal, open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, click <bpt id="p1">**</bpt>Bash<ept id="p1">**</ept> and <bpt id="p2">**</bpt>Create storage<ept id="p2">**</ept>.

3. 确保在 Cloud Shell 窗格左上角的下拉菜单中选中“Bash”。

4. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令为此实验室创建新的资源组：

    ```sh
    az group create --name AZ500LAB09 --location eastus
    ```

5. 在“Cloud Shell”窗格的 Bash 会话中，运行以下命令以验证资源组是否已创建：

    ```
    az group list --query "[?name=='AZ500LAB09']" -o table
    ```

6. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以创建新的 Azure 容器注册表 (ACR) 实例（ACR 的名称必须是全局唯一的）： 

    ```sh
    az acr create --resource-group AZ500LAB09 --name az500$RANDOM$RANDOM --sku Basic
    ```

7. 在“Cloud Shell”窗格的 Bash 会话中，运行以下命令以确认新的 ACR 已创建：

    ```sh
    az acr list --resource-group AZ500LAB09
    ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record the name of the ACR. You will need it in the next task.

#### <a name="task-2-create-a-dockerfile-build-a-container-and-push-it-to-azure-container-registry"></a>任务 2：创建 Dockerfile、构建容器并将其推送到 Azure 容器注册表

在此任务中，你将创建 Dockerfile、从 Dockerfile 构建映像，以及将映像部署到 ACR。 

1. 在“Cloud Shell”窗格中的 Bash 会话中，通过运行以下命令创建 Dockerfile，以创建基于 Nginx 的映像： 

    ```sh
    echo FROM nginx > Dockerfile
    ```

2. 在 Cloud Shell 窗格中的 Bash 会话中，运行以下命令以从 Dockerfile 生成映像，并将该映像推送到新的 ACR。 

    >你需要使用 Azure 容器注册表和 Azure Kubernetes 服务部署概念证明。 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az acr build --resource-group AZ500LAB09 --image sample/nginx:v1 --registry $ACRNAME --file Dockerfile .
    ```

    >具体而言，概念证明应证明：

3. 关闭 Cloud Shell 窗格。

4. 在 Azure 门户中，导航到 AZ500Lab09 资源组，然后在资源列表中，单击表示你在上一个任务中预配的 Azure 容器注册表实例的条目。

5. 在“容器注册表”边栏选项卡的“服务”部分，单击“存储库”。 

6. 验证存储库列表是否包含名为 sample/nginx 的新容器映像。

7. 单击“sample/nginx”条目并验证是否存在标识映像版本的 v1 标记。

8. 单击“v1”条目以查看映像清单。

    >**注意**：清单包括 sha256 摘要、清单创建日期和平台条目。 

#### <a name="task-3-create-an-azure-kubernetes-service-cluster"></a>任务 3：创建 Azure Kubernetes 服务群集

在此任务中，你将创建 Azure Kubernetes 服务并查看已部署的资源。 

1. 在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“Kubernetes 服务”，然后按“Enter”键。

2. 在“Kubernetes 服务”边栏选项卡上，单击“+ 创建”，然后在下拉菜单中单击“+ 创建 Kubernetes 群集”

3. On the <bpt id="p1">**</bpt>Basics<ept id="p1">**</ept> tab of the <bpt id="p2">**</bpt>Create Kubernetes cluster<ept id="p2">**</ept> blade, select <bpt id="p3">**</bpt>Cluster preset configuration<ept id="p3">**</ept>, select <bpt id="p4">**</bpt>Dev/Test ($)<ept id="p4">**</ept>. Now specify the following settings (leave others with their default values):

    |设置|值|
    |----|----|
    |订阅|你在此实验室中使用的 Azure 订阅的名称|
    |资源组|AZ500LAB09|
    |Kubernetes 群集名称|MyKubernetesCluster|
    |区域|**（美国）美国东部**|
    |可用性区域 |无 |
    |缩放方法|**手动**|
    |节点计数|**1**|

4. 单击“下一步:节点池 >”，然后在“创建 Kubernetes 群集”边栏选项卡的“节点池”选项卡上，指定以下设置（将其他设置保留为默认值）：

    |设置|值|
    |----|----|
    |启用虚拟节点|清除复选框|
    
5. 单击“下一步:**访问权限 >”，在“创建 Kubernetes 群集”边栏选项卡的“访问权限”选项卡上，接受默认设置，然后单击“下一步:**   网络 >”。 

6. 在“创建 Kubernetes 群集”边栏选项卡的“网络”选项卡中，指定以下设置（其他设置保留默认值） ：

    |设置|值|
    |----|----|
    |网络配置|**Azure CNI**|
    |DNS 名称前缀|**保留默认值**|

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: AKS can be configured as a private cluster. This assigns a private IP to the API server to ensure network traffic between your API server and your node pools remains on the private network only. For more information, visit <bpt id="p1">[</bpt>Create a private Azure Kubernetes Service cluster<ept id="p1">](https://docs.microsoft.com/en-us/azure/aks/private-clusters)</ept> page.

7. 单击“下一步:集成 >”，然后在“创建 Kubernetes 群集”边栏选项卡的“集成”选项卡上，将“容器监视”设置为“禁用”。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: In production scenarios, you would want to enable monitoring. Monitoring is disabled in this case since it is not covered in the lab. 

8. 单击“查看 + 创建”，然后单击“创建”。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. This might take about 10 minutes.

9. 部署完成后，在 Azure 门户页面顶部的“搜索资源、服务和文档”文本框中，键入“资源组”，然后按 Enter 键  。

10. On the <bpt id="p1">**</bpt>Resource groups<ept id="p1">**</ept> blade, in the listing of resource groups, note a new resource group named <bpt id="p2">**</bpt>MC_AZ500LAB09_MyKubernetesCluster_eastus<ept id="p2">**</ept> that holds components of the AKS Nodes. Review resources in this resource group. 
    
11. 导航回“资源组”边栏选项卡，然后单击“AZ500LAB09”条目。 

    >**注意**：在资源列表中，记下 AKS 群集和相应的虚拟网络。

12. 在 Azure 门户中，在 Cloud Shell 中打开 Bash 会话。 

    >**注意**：确保在 Cloud Shell 窗格左上角的下拉菜单中选中“Bash”。

13. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以连接到 Kubernetes 群集：

    ```sh
    az aks get-credentials --resource-group AZ500LAB09 --name MyKubernetesCluster
    ```

14. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以列出 Kubenetes 群集的节点： 

    ```sh
    kubectl get nodes
    ```

    >**注意**：验证列出的群集节点状态是否为“就绪” 。

#### <a name="task-4-grant-the-aks-cluster-permissions-to-access-the-acr-and-manage-its-virtual-network"></a>任务 4：授予 AKS 群集访问 ACR 和管理 ACR 虚拟网络的权限

在该任务中，你将授予 AKS 群集访问 ACR 和管理 ACR 虚拟网络的权限。 

1. 在“Cloud Shell”窗格的 Bash 会话中，运行以下命令以将 AKS 群集配置为使用在本实验室前面部分创建的 Azure 容器注册表实例。 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az aks update -n MyKubernetesCluster -g AZ500LAB09 --attach-acr $ACRNAME
    ```

    >**注意**：此命令将“acrpull”角色分配授予 ACR。 

    >**注意**：此命令需要几分钟时间才能完成操作。 

2. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以授予 AKS 群集对其虚拟网络的“参与者”角色。 

    ```sh
    RG_AKS=AZ500LAB09
    
    AKS_VNET_NAME=AZ500LAB09-vnet
    
    AKS_CLUSTER_NAME=MyKubernetesCluster
    
    AKS_VNET_ID=$(az network vnet show --name $AKS_VNET_NAME --resource-group $RG_AKS --query id -o tsv)
    
    AKS_MANAGED_ID=$(az aks show --name $AKS_CLUSTER_NAME --resource-group $RG_AKS --query identity.principalId -o tsv)
    
    az role assignment create --assignee $AKS_MANAGED_ID --role "Contributor" --scope $AKS_VNET_ID
    ```

#### <a name="task-5-deploy-an-external-service-to-aks"></a>任务 5：将外部服务部署到 AKS

在此任务中，你将下载清单文件、编辑 YAML 文件，并将更改应用到群集。 

1. 对于本实验室中的所有资源，我们使用“美国东部”区域。

2. 在“Cloud Shell”窗格的 Bash 会话中，运行以下命令以标识 Azure 容器注册表实例的名称：

    ```sh
    echo $ACRNAME
    ```

    >请与讲师确认这是课堂上所使用的区域。

3. 在 Cloud Shell 窗格中的 Bash 会话中，运行以下命令以打开 nginxexternal.yaml 文件，以便你可以编辑其内容。 

    ```sh
    code ./nginxexternal.yaml
    ```

    >**注意**：这是外部 yaml 文件。

4. 在编辑器窗格中，向下滚动到第 24 行，并将 `<ACRUniquename>` 占位符替换为 ACR 名称。

5. 在编辑器窗格的右上角，单击省略号图标，单击“保存”，然后单击“关闭编辑器”。 

6. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令将更改应用于群集：

    ```sh
    kubectl apply -f nginxexternal.yaml
    ```

7. 在 Cloud Shell 窗格中的 Bash 会话中，查看你在上一个任务中运行命令的输出，验证已创建的部署和相应的服务。 

    ```
    deployment.apps/nginxexternal created
    service/nginxexternal created
    ```

#### <a name="task-6-verify-the-you-can-access-an-external-aks-hosted-service"></a>任务 6：验证你是否可以访问外部 AKS 托管的服务

在此任务中，验证是否可以使用公共 IP 地址从外部访问容器。

1. 在 Cloud Shell 窗格中的 Bash 会话中，运行以下命令以检索有关 nginxexternal 服务的信息，包括名称、类型、IP 地址和端口。 

    ```sh
    kubectl get service nginxexternal
    ```

2. In the Bash session within the Cloud Shell pane, review the output and record the value in the External-IP column. You will need it in the next step. 

3. 打开新的浏览器标签页，浏览到你在上一步中标识的 IP 地址。

4. Ensure the <bpt id="p1">**</bpt>Welcome to nginx!<ept id="p1">**</ept> page displays. 

#### <a name="task-7-deploy-an-internal-service-to-aks"></a>任务 7：将内部服务部署到 AKS

在此任务中，你将在 AKS 上部署面向内部的服务。 

1. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以打开 nginxintenal.yaml 文件，以便编辑其内容。 

    ```sh
    code ./nginxinternal.yaml
    ```

    >**注意**：这是内部 yaml 文件。

2. 在编辑器窗格中，向下滚动到包含对容器映像的引用的行，并将 `<ACRUniquename>` 占位符替换为 ACR 名称。

3. 在编辑器窗格的右上角，单击省略号图标，单击“保存”，然后单击“关闭编辑器”。 

4. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令将更改应用于群集：

    ```sh
    kubectl apply -f nginxinternal.yaml
    ```

5.  在“Cloud Shell”窗格中的 Bash 会话中，查看输出以验证是否已创建部署和服务：

    ```
    deployment.apps/nginxinternal created
    service/nginxinternal created
    ```

6. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以检索有关 nginxinternal 服务的信息，包括名称、类型、IP 地址和端口。 

    ```sh
    kubectl get service nginxinternal
    ```

7. In the Bash session within the Cloud Shell pane, review the output. The External-IP is, in this case, a private IP address. If it is in a <bpt id="p1">**</bpt>Pending<ept id="p1">**</ept> state then run the previous command again.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Record this IP address. You will need it in the next task. 

    >**注意**：要访问内部服务终结点，需要以交互方式连接到群集中运行的其中一个 Pod。 

    >**注意**：或者可以使用 CLUSTER-IP 地址。

#### <a name="task-8-verify-the-you-can-access-an-internal-aks-hosted-service"></a>任务 8：验证你是否可以访问 AKS 托管的内部服务

在此任务中，你将使用在 AKS 群集上运行的 Pod 之一来访问内部服务。 

1. 在“Cloud Shell”窗格中的 Bash 会话中，运行以下命令以列出 AKS 群集上默认命名空间中的 Pod：

    ```sh
    kubectl get pods
    ```

2. 在 Pod 列表中，复制“名称”列中的第一个条目。

    >**注意**：这是你在后续步骤中要使用的 Pod。

3. 在 Cloud Shell 窗格的 Bash 会话中，运行以下命令，以交互方式连接到第一个 Pod（将 `<pod_name>` 占位符替换为上一步中复制的名称）：

    ```sh
    kubectl exec -it <pod_name> -- /bin/bash
    ```

4. 在 Cloud Shell 窗格的 Bash 会话中，运行以下命令，验证 nginx 网站是否可通过服务的专用 IP 地址访问（将 `<internal_IP>` 占位符替换为上一个任务中记录的 IP 地址）：

    ```sh
    curl http://<internal_IP>
    ```

5. 关闭 Cloud Shell 窗格。

> 结果：你已对 ACR 和 AKS 进行了配置和保护。


**清理资源**

> Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs.

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 

2. 在 Cloud Shell 窗格的左上下拉菜单中，选择“PowerShell”，并在出现提示时单击“确认” 。

3. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以删除你在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB09" -Force -AsJob
    ```

4.  关闭 Cloud Shell 窗格。 
