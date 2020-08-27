---
title: Escolher entre o Gerenciamento de Custos do Azure e o Cloudyn
description: Este artigo ajuda você a determinar qual entre o Gerenciamento de Custos do Azure e o Cloudyn é melhor para suas necessidades de gerenciamento de custos.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684959"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Escolher entre o Gerenciamento de Custos do Azure e o Cloudyn

O Cloudyn será preterido até o final de 2020. Os recursos do Cloudyn existentes estão sendo integrados diretamente no portal do Azure, sempre que possível. Com exceção dos clientes do CSP, nenhum novo cliente pode ingressar no momento. O suporte para o produto existente permanecerá até que ele seja totalmente preterido.

A Microsoft adquiriu o Cloudyn e está migrando seus recursos de gerenciamento de custo do portal do Cloudyn nativamente no Azure. Para usar os novos recursos, entre no portal do Azure e navegue até [Gerenciamento de Custos e Cobrança](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na lista de serviços do Azure. Em comparação ao Cloudyn, a experiência nativa oferece melhor desempenho e menor latência de dados de cerca de oito horas.

A migração de chave de recurso para as categorias de oferta Contrato Enterprise, Pagamento Conforme o Uso e MSDN para o Gerenciamento de Custos do Azure foi concluída. As assinaturas de CSP estão no processo que está sendo migrado para o Gerenciamento de Custos do Azure.

Se você tiver uma categoria de oferta que ainda não foi migrada, continue a continuar a usar o portal do Cloudyn. Qualquer outra pessoa pode usar o Gerenciamento de Custos do Azure.

## <a name="recommended-services-by-offer"></a>Serviços recomendados por oferta

| Ofertas do Microsoft Azure | Serviço de gerenciamento de custo recomendado |
| --- | --- |
| Contrato Enterprise do Azure | [Gerenciamento de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Gerenciamento de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Gerenciamento de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Contrato de Cliente da Microsoft | [Gerenciamento de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Contrato de Cliente da Microsoft compatível para parceiros | [Gerenciamento de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Recursos de Gerenciamento de Custos disponíveis

Alguns dos recursos a seguir estão disponíveis no Cloudyn, mas todos eles estão disponíveis no Gerenciamento de Custos do Azure.

- APIs
- Recomendações de otimização de custos do Azure, incluindo, mas não se limitando a:
    - Recomendações de dimensionamento e desligamento correto da instância do Azure
    - Recomendações de reserva do Azure
- Orçamentos
- Análise de custo
- Exportar dados em uma conta do Armazenamento do Azure
- Menor latência
- Aplicativo de modelo do Power BI
- Suporte de marca do recurso
- Suporte de análise de custo entre nuvens para AWS

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Gerenciamento de Custos do Azure](../cost-management-billing-overview.md).