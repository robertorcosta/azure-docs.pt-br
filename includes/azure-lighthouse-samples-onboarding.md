---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986610"
---
Fornecemos diferentes modelos para abordar diferentes cenários de integração. Escolha a opção que funciona melhor e modifique o arquivo de parâmetro para refletir seu ambiente. Para obter mais informações sobre como usar esses arquivos em sua implantação, confira [Integrar um cliente ao gerenciamento de recursos delegados do Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Integra uma assinatura de cliente ao gerenciamento de recursos delegados do Azure. Uma implantação separada deve ser executada para cada assinatura. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Integra um ou mais dos grupos de recursos do cliente ao gerenciamento de recursos delegados do Azure. Use **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma assinatura. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Se você [publicou uma oferta de serviços gerenciados no Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), pode opcionalmente usar esse modelo para integrar recursos para clientes que aceitaram a oferta. Os valores de marketplace no arquivo de parâmetros devem corresponder aos valores que você usou ao publicar sua oferta. |

Normalmente, uma implantação separada é necessária para cada assinatura que está sendo integrada, mas você também pode implantar modelos em várias assinaturas.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implanta modelos do Azure Resource Manager entre várias assinaturas. |
