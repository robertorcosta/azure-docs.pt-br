---
title: Políticas para etiquetagem de recursos
description: Descreve as políticas do Azure que você pode atribuir para garantir a conformidade da tag.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147012"
---
# <a name="assign-policies-for-tag-compliance"></a>Atribuir políticas para conformidade de tag

Você usa [a Política Do Azure](../../governance/policy/overview.md) para impor regras de marcação e convenções. Ao criar uma política, você evita o cenário de recursos sendo implantados em sua assinatura que não têm as tags esperadas para sua organização. Em vez de aplicar manualmente tags ou procurar recursos que não estão em conformidade, você cria uma diretiva que aplica automaticamente as tags necessárias durante a implantação. As tags também podem ser aplicadas aos recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma [tarefa de remediação](../../governance/policy/how-to/remediate-resources.md). A seção a seguir mostra as políticas de exemplo para marcas.

## <a name="policies"></a>Políticas

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a marcação de recursos, confira [Usar marcas para organizar os recursos do Azure](tag-resources.md).
* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
