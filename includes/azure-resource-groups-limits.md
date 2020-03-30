---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334943"
---
| Recurso | Limite |
| --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | Os recursos não são limitados por grupo de recursos. Em vez disso, eles são limitados por tipo de recurso em um grupo de recursos. Veja a próxima fila. |
| Recursos por grupo de recursos, por tipo de recurso |800 - Alguns tipos de recursos podem exceder o limite de 800. Consulte [Recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implantações por grupo de recursos no histórico de implantações |800<sup>1</sup> |
| Recursos por implantação |800 |
| Bloqueios de gerenciamento por escopo único |20 |
| Número de tags por grupo de recursos ou recursos |50 |
| Comprimento da chave de marca |512 |
| Comprimento do valor da marca |256 |

<sup>1</sup> Se você atingir o limite de 800 implantações por grupo de recursos, exclua implantações do histórico que não são mais necessárias. A exclusão de uma entrada do histórico de implantação não afeta os recursos implantados. Para obter mais informações, consulte [Resolver erro quando a contagem de implantação exceder 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limites de modelo

| Valor | Limite |
| --- | --- |
| Parâmetros |256 |
| Variáveis |256 |
| Recursos (incluindo a contagem de cópias) |800 |
| outputs |64 |
| Expressão de modelo |24.576 caracteres |
| Recursos em modelos exportados |200 |
| Tamanho do modelo |4 MB |
| Tamanho do arquivo de parâmetro |64 KB |

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [Usar modelos vinculados ao implantar recursos do Azure](../articles/azure-resource-manager/templates/linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
