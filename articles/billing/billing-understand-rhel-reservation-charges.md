---
title: 了解 Red Hat 预订计划折扣和使用情况-Azure |Microsoft Docs
description: 了解如何将 Red Hat 计划折扣应用于虚拟机上的 Red Hat 软件。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370196"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>了解如何为 Azure 应用 Red Hat Linux Enterprise 软件预订计划折扣

购买的 Red Hat Linux 计划后，折扣将自动应用到已部署 Red Hat 虚拟机 (Vm) 与预订匹配。 Red Hat Linux 计划涵盖 Azure VM 上运行 Red Hat 软件的成本。

若要购买适当的 Red Hat Linux 计划，您需要了解哪些你运行的 Red Hat Vm 以及这些 vm Vcpu 的数量。 使用下列各节来帮助通过使用情况 CSV 文件来识别要购买的计划。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣适用于不同的 VM 大小

保留 VM 实例，如 Red Hat 计划购买产品/服务实例大小灵活性。 这意味着即使在部署 vCPU 计数不同的 VM 时，折扣也适用。 折扣适用于软件计划内不同的 VM 大小。

折扣金额取决于下表中列出的比率。 比率将比较该组中每个计量的相对占用情况。 比率取决于 VM vCPU。 使用的比率值来计算多少个 VM 实例获取 Red Hat Linux 计划折扣。

例如，如果具有 3 个或 4 个 Vcpu 的 vm，Red Hat Linux Enterprise server 购买计划，为该预订比率将是 2。 折扣涵盖的 Red Hat 软件成本：

- 2 台具有 1 或 2 个 vCPU 的已部署 VM，
- 1 台具有 3 或 4 个 vCPU 的已部署 VM，
- 或为具有 5 个或更多 vCPU 的 VM 的 0.77 或约 77%。

对于 5 个或更多的 vCPU，此比率为 2.6。 因此与具有 5 个或更多 Vcpu 的 VM 的 Red hat 预订涵盖软件成本，大约 77%具有唯一的部分。

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>了解 Red Hat VM 使用情况，在购买前

下表显示可为其购买预订的软件计划、其相关的使用情况计量和每个计划的比率。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure 门户商城名称：

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (最新 lvm)

|Red Hat VM | 计量 ID| 比率| 示例 VM 大小|
| -------| ------------------------| --- |--- |
|1-4 vCPU VM 许可证|077a07bb-20f8-4bc6-b596-ab7211a1e247|第|D4s_v3|
|1-4 vCPU VM 许可证|2f96d035-3bac-46d6-b2bc-c6daa0938536|第|D4s_v3|
|1-4 vCPU VM 许可证|4831a7b4-bdd4-48a2-8e95-18d053971ede|第|D4s_v3|
|5 + vCPU VM 许可证|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 + vCPU VM 许可证|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|44 vCPU VM 许可证|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 个 vCPU VM 许可证|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP 高可用性与

Azure 门户商城名称：

|Red Hat VM | 计量 ID | 比率|示例 VM 大小|
| ------- | --- | ------------------------| --- | --- |
|1-4 vCPU VM 许可证 |4d902611-eed7-4060-a33e-3c7fdbac6406|第|D4s_v3|
|5 + vCPU VM 许可证|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux（带 HA）

Azure 门户商城名称：

|Red Hat VM | 计量 ID | 比率|示例 VM 大小|
| ------- |------------------------| --- | --- |
|1-4 vCPU VM 许可证|e9711132-d9d9-450c-8203-25cfc4bce8de|第|D4s_v3|
|5 + vCPU VM 许可证|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure 门户商城名称：

- Red Hat Enterprise Linux 6.8 SAP 业务应用程序
- SAP 业务应用程序的 Red Hat Enterprise Linux 7.3
- 适用于 SAP 的 Red Hat Enterprise Linux 7.4
- 适用于 SAP 的 Red Hat Enterprise Linux 7.5

|Red Hat VM | 计量 ID | 比率|示例 VM 大小|
| ------- |------------------------| --- |--- |
|1 个 vCPU VM 许可证|25889e91-c740-42ac-bc52-6b8f73b98575|第|D2s_v3|
|2 个 vCPU VM 许可证|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|第|D2s_v3|
|4 个 vCPU VM 许可证|875898d3-3639-423c-82c1-38846281b7e8|第|D4s_v3|
|6 vCPU VM 许可证|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 个 vCPU VM 许可证|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 个 vCPU VM 许可证|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 vCPU VM 许可证|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 个 vCPU VM 许可证|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 个 vCPU VM 许可证|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 个 vCPU VM 许可证|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 个 vCPU VM 许可证|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|44 vCPU VM 许可证|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 个 vCPU VM 许可证|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 vCPU VM 许可证|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 vCPU VM 许可证|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 vCPU VM 许可证|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 个 vCPU VM 许可证|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure 门户商城名称：

- SAP HANA 的 Red Hat Enterprise Linux 6.7
- SAP HANA 的 Red Hat Enterprise Linux 7.2
- SAP HANA 的 Red Hat Enterprise Linux 7.3

|Red Hat VM | 计量 ID | 比率|示例 VM 大小|
| ------- |------------------------| --- |--- |
|1 个 vCPU VM 许可证|be0a59d1-eed7-47ec-becd-453267753793|第|D2s_v3|
|2 个 vCPU VM 许可证|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|第|D2s_v3|
|4 个 vCPU VM 许可证|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|第|D4s_v3|
|6 vCPU VM 许可证|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 个 vCPU VM 许可证|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 个 vCPU VM 许可证|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 vCPU VM 许可证|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 个 vCPU VM 许可证|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 个 vCPU VM 许可证|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 个 vCPU VM 许可证|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 个 vCPU VM 许可证|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|44 vCPU VM 许可证|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 个 vCPU VM 许可证|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 vCPU VM 许可证|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 vCPU VM 许可证|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 vCPU VM 许可证|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 个 vCPU VM 许可证|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>后续步骤

若要了解有关预留的详细信息，请参阅以下文章：

- [什么是适用于 Azure 的预留](billing-save-compute-costs-reservations.md)
- [预付款购买的 Red Hat 软件计划的 Azure 预订](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 的保留项](billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
