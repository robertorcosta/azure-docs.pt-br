---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 226079b1970508004c55c54cd3de6d2e9475aa08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75970120"
---
Após aplicar as marcas aos recursos, será possível exibir os custos dos recursos com essas marcas. Demora um tempo para que a análise de custo mostre o uso mais recente, portanto, talvez ainda não seja possível exibir os custos. Quando os custos estiverem disponíveis, você poderá visualizá-los nos grupos de recursos em sua assinatura. Para visualizar os custos, os usuários deverão ter [acesso no nível da assinatura para informações de cobrança](../articles/cost-management-billing/manage/manage-billing-access.md).

Para exibir custos por marca no portal, selecione sua assinatura e, em seguida, **Análise de Custo**.

![Análise de custo](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre pelo valor de marca e selecione **Aplicar**.

![Exibir custo por marca](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também é possível usar as [APIs de Cobrança do Azure](../articles/cost-management-billing/manage/usage-rate-card-overview.md) para exibir os custos de forma programática.
