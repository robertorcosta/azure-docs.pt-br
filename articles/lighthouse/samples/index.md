---
title: Exemplos e modelos do Azure Lighthouse
description: Esses exemplos e os modelos do Azure Resource Manager mostram como integrar clientes para gerenciamento de recursos delegados do Azure e dão suporte a cenários do Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286144"
---
# <a name="azure-lighthouse-samples"></a>Exemplos do Azure Lighthouse

A tabela a seguir inclui links para os principais modelos do Azure Resource Manager para o Azure Lighthouse. Esses arquivos e muito mais também podem ser encontrados na [repositório de exemplos do Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

| **Modelo** | **Descrição** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | Cria vários grupos de recursos usando um único modelo do Azure Resource Manager. |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Implanta contas de armazenamento em dois grupos de recursos diferentes. |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implanta modelos do Azure Resource Manager entre várias assinaturas. |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Integra uma assinatura de cliente ao gerenciamento de recursos delegados do Azure. |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Cria serviços de gerenciamento do Azure, vincula-os e implanta soluções adicionais. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Habilita e configura a Central de Segurança do Azure na assinatura de destino do Azure. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Integra a assinatura de um cliente ao gerenciamento de recursos delegados do Azure com base em uma oferta de serviços gerenciados publicada no marketplace. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Integra um ou mais dos grupos de recursos do cliente ao gerenciamento de recursos delegados do Azure. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md).
- Saiba mais sobre como usar modelos do Azure Resource Manager para [integrar clientes no gerenciamento de recursos delegados do Azure](../how-to/onboard-customer.md).
