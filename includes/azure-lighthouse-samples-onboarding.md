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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456765"
---
Fornecemos diferentes modelos para abordar diferentes cenários de integração. Escolha a opção que funciona melhor e modifique o arquivo de parâmetro para refletir seu ambiente. Para obter mais informações sobre como usar esses arquivos em sua implantação, confira [Integrar um cliente ao gerenciamento de recursos delegados do Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Integra uma assinatura de cliente ao gerenciamento de recursos delegados do Azure. Uma implantação separada deve ser executada para cada assinatura. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Integra um ou mais dos grupos de recursos do cliente ao gerenciamento de recursos delegados do Azure. Use **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma assinatura. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se você [publicou uma oferta de serviços gerenciados no Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), pode opcionalmente usar esse modelo para integrar recursos para clientes que aceitaram a oferta. Os valores de marketplace no arquivo de parâmetros devem corresponder aos valores que você usou ao publicar sua oferta. |

Normalmente, uma implantação separada é necessária para cada assinatura que está sendo integrada, mas você também pode implantar modelos em várias assinaturas.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implanta modelos do Azure Resource Manager entre várias assinaturas. |
