---
lab:
  title: 09 - 配置适用于服务器的 Microsoft Defender for Cloud 增强的安全性功能
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# 实验室 09：配置适用于服务器的 Microsoft Defender for Cloud 增强的安全性功能

# 学生实验室手册

## 实验室方案

作为一家全球性电子商务公司的 Azure 安全工程师，你负责保护公司的云基础结构。 组织严重依赖 Azure 虚拟机 (VM) 和本地服务器来运行关键应用程序、管理客户数据和处理事务。 首席信息安全官 (CISO) 已确定需要加强安全措施，以保护这些资源免受网络威胁、漏洞和错误配置的影响。 你一直负责在 Microsoft Defender for Cloud 中启用 Microsoft Defender for Servers，以便为 Azure VM 和混合服务器提供高级威胁防护和安全监视。

## 实验室目标

- 配置适用于服务器的 Microsoft Defender for Cloud 增强安全功能
  
- 查看适用于 Microsoft Defender for Servers 计划 2 的增强安全功能

## 练习说明

### 配置适用于服务器的 Microsoft Defender for Cloud 增强安全功能

1. 在 Azure 门户中，在 Azure 门户页面顶部的搜索资源、服务和文档文本框中，键入 **Microsoft Defender for Cloud**，然后按 **Enter** 键。

2. 在 **Microsoft Defender for Cloud** 的“**管理边栏选项卡**”上，转到“**环境设置**”。 展开环境设置文件夹，直到显示**订阅**部分，然后单击“**订阅**”以查看详细信息。

   ![image](https://github.com/user-attachments/assets/3b25dd82-e09e-4f8a-b85e-c9bc6c4bd488)
   
3. 在“**设置**”边栏选项卡中的“**Defender 计划**”下，展开**云工作负荷保护 (CWP) **。

4. 从**云工作负荷保护 (CWP) 计划**列表中，选择“**服务器**”。 在页面右侧，将**状态**从“**关**”更改为“**开**”，然后单击“**保存**”。

5. 若要查看 **Microsoft Defender for Servers 计划 2** 的详细信息，请选择“**更改计划 >**”。

   备注：将云工作负载保护 (CWP) 服务器计划从“关”启用为“开”，可启用 Microsoft Defender for Servers 计划 2。
 
   ![image](https://github.com/user-attachments/assets/de434a75-345a-4023-83f1-fa53fcb5f288)
   
> **结果**：已在订阅上启用 Microsoft Defender for Servers 计划 2。
