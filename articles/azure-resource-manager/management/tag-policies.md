---
title: Políticas para a marcação de recursos
description: Descreve as políticas do Azure que você pode atribuir para garantir a conformidade da marca.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147860"
---
# <a name="assign-policies-for-tag-compliance"></a>Atribuir políticas de conformidade de marca

Você usa [Azure Policy](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, você evita o cenário dos recursos que estão sendo implantados em sua assinatura que não têm as marcas esperadas para sua organização. Em vez de aplicar marcas manualmente ou procurar recursos que não são compatíveis, você cria uma política que aplica automaticamente as marcas necessárias durante a implantação. Agora, as marcas também podem ser aplicadas a recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma [tarefa de correção](../../governance/policy/how-to/remediate-resources.md). A seção a seguir mostra as políticas de exemplo para marcas.

## <a name="policies"></a>Políticas

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a marcação de recursos, confira [Usar marcas para organizar os recursos do Azure](tag-resources.md).
* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
