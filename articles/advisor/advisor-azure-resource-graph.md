---
title: Dados do Advisor no grafo de recursos do Azure
description: Fazer consultas para dados do Advisor no grafo de recursos do Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502445"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Consultar dados do Advisor no Gerenciador de grafo de recursos (grafo de recursos do Azure)

Os recursos do Advisor agora são integrados ao [grafo de recursos do Azure](https://azure.microsoft.com/features/resource-graph/). Isso estabelece a base para muitos cenários de clientes em escala para as recomendações do Advisor. Poucos cenários que não eram possíveis antes de fazer em escala e agora podem ser obtidos usando o grafo de recursos são:
* Fornece a capacidade de executar consultas complexas para todas as suas assinaturas no portal do Azure
* Recomendações resumidas por tipos de categoria (como alta disponibilidade, desempenho) e tipos de impacto (alta, média, baixa)
* Todas as recomendações para um tipo de recomendação específico
* Contagem de recursos impactados por categoria de recomendação

![Assistente no Gerenciador de grafo de recursos do Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Tipos de recursos do Advisor no grafo do Azure

Tipos de recursos do Advisor disponíveis no [grafo de recursos](https://docs.microsoft.com/azure/governance/resource-graph/): há três tipos de recursos disponíveis para consulta de recursos do Advisor. Aqui está a lista dos recursos que agora estão disponíveis para consulta no grafo de recursos.
* Microsoft. Advisor/Configurations
* Microsoft. Advisor/Recommendations
* Microsoft. Advisor/supressões

Esses tipos de recursos são listados em uma nova tabela chamada como AdvisorResources, que você também pode consultar no Gerenciador de grafo de recursos em portal do Azure.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as recomendações do Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
* [API REST do Advisor](https://docs.microsoft.com/rest/api/advisor/)
