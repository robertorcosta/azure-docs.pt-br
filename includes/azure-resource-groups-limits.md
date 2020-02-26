---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c1a52e1bc2dde74289cb270fcae832be24de5a06
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592402"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Recursos por [grupo de recursos](../articles/azure-resource-manager/management/overview.md#resource-groups) | N/D | Os recursos não são limitados pelo grupo de recursos. Em vez disso, eles são limitados pelo tipo de recurso em um grupo de recursos. Consulte a próxima linha. | 
| Recursos por grupo de recursos, por tipo de recurso |800 |Alguns tipos de recursos podem exceder o limite de 800. Consulte [recursos não limitados a 800 instâncias por grupo de recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implantações por grupo de recursos no histórico de implantações |800<sup>1</sup> |800 |
| Recursos por implantação |800 |800 |
| Bloqueios de gerenciamento por escopo exclusivo |20 |20 |
| Número de marcas por recurso ou grupo de recursos |50 |50 |
| Comprimento da chave de marca |512 |512 |
| Comprimento do valor da marca |256 |256 |

<sup>1</sup> Se você atingir o limite de 800 implantações por grupo de recursos, exclua as implantações do histórico que não são mais necessárias. A exclusão de uma entrada do histórico de implantação não afeta os recursos implantados. Para obter mais informações, consulte [resolver erro quando a contagem de implantação exceder 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limites de modelo

| Valor | Limite padrão | Limite máximo |
| --- | --- | --- |
| parâmetros |256 |256 |
| variáveis |256 |256 |
| Recursos (incluindo a contagem de cópias) |800 |800 |
| outputs |64 |64 |
| Expressão de modelo |24.576 caracteres |24.576 caracteres |
| Recursos em modelos exportados |200 |200 | 
| Tamanho do modelo |4 MB |4 MB |
| Tamanho do arquivo de parâmetro |64 KB |64 KB |

Você pode exceder alguns limites de modelo usando um modelo aninhado. Para obter mais informações, consulte [usar modelos vinculados ao implantar recursos do Azure](../articles/azure-resource-manager/templates/linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, você pode combinar vários valores em um objeto. Para saber mais, veja [Objetos como parâmetros](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
