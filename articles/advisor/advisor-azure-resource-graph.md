---
title: Dados do Advisor no Gráfico de Recursos do Azure
description: Faça consultas para dados do Advisor no Gráfico de Recursos do Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502445"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Consulta aos dados do Advisor no Resource Graph Explorer (Gráfico de Recursos do Azure)

Os recursos do advisor estão agora a bordo [do Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Isso estabelece bases para muitos cenários de clientes em escala para recomendações do Advisor. Poucos cenários que não eram possíveis antes de serem realizados em escala e agora podem ser alcançados usando o Resource Graph são:
* Dá capacidade para realizar consultas complexas para todas as suas assinaturas no portal Azure
* Recomendações resumidas por tipos de categorias (como alta disponibilidade, desempenho) e tipos de impacto (alto, médio, baixo)
* Todas as recomendações para um tipo de recomendação específica
* Contagem de recursos impactados por categoria de recomendação

![Consultor no explorador de gráficos de recursos do Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipos de recursos do Advisor no Gráfico Azure

Tipos de recursos do Advisor disponíveis no [Gráfico de Recursos](https://docs.microsoft.com/azure/governance/resource-graph/): Existem 3 tipos de recursos disponíveis para consulta recursos do Advisor. Aqui está a lista dos recursos que agora estão disponíveis para consulta no Resource Graph.
* Microsoft.Advisor/configurações
* Microsoft.Advisor/recomendações
* Microsoft.Advisor/supressões

Esses tipos de recursos estão listados em uma nova tabela chamada AdvisorResources, que você também pode consultar no Resource Graph Explorer no portal Azure.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional do Orientador](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
