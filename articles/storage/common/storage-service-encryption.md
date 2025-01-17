---
title: 静态数据的 Azure 存储加密 | Microsoft Docs
description: Azure 存储在将数据保存到云之前会自动对其进行加密，以此保护数据。 Azure 存储中的所有数据使用 256 位 AES 加密法以透明方式加密和解密，并符合 FIPS 140-2 规范。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1e95adbd1a564fb34d3f0506ac1cc25bc5a63c62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790059"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>静态数据的 Azure 存储加密

Azure 存储在将数据保存到云时会自动加密数据。 加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 Azure 存储中的数据将使用 256 位 [AES 加密法](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。

将针对所有新的和现有的存储帐户启用 Azure 存储加密，并且不能禁用加密。 由于数据默认受到保护，因此无需修改代码或应用程序，即可利用 Azure 存储加密。 

不管存储帐户的性能层（标准或高级）或部署模型（Azure 资源管理器或经典）是什么，都会将其加密。 所有 Azure 存储冗余选项都支持加密，存储帐户的所有副本都会加密。 所有 Azure 存储资源（包括 Blob、磁盘、文件、队列和表）都会加密。 所有对象元数据也会加密。

加密不影响 Azure 存储的性能。 Azure 存储加密不会产生额外的费用。

有关 Azure 存储加密的底层加密模块的详细信息，请参见[加密 API：下一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="key-management"></a>密钥管理

您可以依赖于 Microsoft 托管密钥进行加密的存储帐户，或使用你自己的密钥，与 Azure 密钥保管库一起加密。

### <a name="microsoft-managed-keys"></a>Microsoft 托管密钥

默认情况下，你的存储帐户使用 Microsoft 托管的加密密钥。 可以在 [Azure 门户](https://portal.azure.com)的“加密”部分查看存储帐户的加密设置，如下图所示。 

![查看使用 Microsoft 托管密钥加密的帐户](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>客户管理的密钥

可以使用客户管理的密钥来管理 Azure 存储加密。 使用客户管理的密钥可以灵活创建、轮换、禁用和撤销访问控制权。 还可以审核用于保护数据的加密密钥。 

使用 Azure Key Vault 管理密钥并审核密钥用法。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

若要撤销对客户管理的密钥的访问权限，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 和 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤销访问权限会实际阻止对存储帐户中所有数据的访问，因为 Azure 存储帐户无法访问加密密钥。

若要了解如何将客户管理的密钥与 Azure 存储配合使用，请参阅以下文章之一：

- [通过 Azure 门户配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-portal.md)
- [通过 PowerShell 配置客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-powershell.md)
- [在 Azure CLI 中将客户管理的密钥用于 Azure 存储加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客户托管密钥依赖于 Azure 资源的管理的标识 Azure Active Directory (Azure AD) 的一项功能。 传输时订阅从一个 Azure AD 目录与另一个托管的标识不会进行更新和客户托管密钥可能不再起作用。 有关详细信息，请参阅**转让订阅 Azure AD 目录之间**中[常见问题解答和已知的问题的管理 Azure 资源的标识](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。  

> [!NOTE]  
> [Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)不支持客户管理的密钥。

## <a name="azure-storage-encryption-versus-disk-encryption"></a>与磁盘加密的 azure 存储加密

使用 Azure 存储加密，所有 Azure 存储帐户和它们所包含的资源进行加密，包括备份 Azure 虚拟机磁盘的页 blob。 此外，可以使用加密 Azure 虚拟机磁盘[Azure 磁盘加密](../../security/azure-security-disk-encryption-overview.md)。 Azure 磁盘加密使用行业标准[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)在 Windows 上并[Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)提供与 Azure 密钥保管库集成的基于操作系统的加密解决方案在 Linux 上。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../../key-vault/key-vault-overview.md)
