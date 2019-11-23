---
title: Update an existing assignment from the portal
description: Learn about the mechanism for updating an existing blueprint assignment from the portal in Azure Blueprints.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: b4cf03d88103b85bc00dbd815816ead2740f2093
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406390"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de blueprint existente

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

## <a name="updating-assignments"></a>Atualizando atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Blueprints atribuídos** na página à esquerda.

1. Na lista de planos gráficos, clique em atribuição de planta. Then click the **Update assignment** button OR right-click the blueprint assignment and select **Update assignment**.

   ![Update an existing blueprint assignment](../media/update-existing-assignments/update-assignment.png)

1. The **Assign blueprint** page will load pre-filled with all values from the original assignment.
   Você pode alterar a **versão de definição do blueprint**, o estado **Bloquear Atribuição** e qualquer um dos parâmetros dinâmicos que existem na definição do blueprint. Clique em **Atribuir** quando terminar de fazer alterações.

1. Na página de detalhes de atribuição atualizada, veja o novo status. Neste exemplo, adicionamos **Bloqueio** à atribuição.

   ![Updated an existing blueprint assignment - lock mode changed](../media/update-existing-assignments/updated-assignment.png)

1. Explore details about other **Assignment operations** using the drop-down. The table of **Managed resources** updates by selected assignment operation.

   ![Assignment operations of a blueprint assignment](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regras para atualizar atribuições

A implantação das atribuições atualizadas segue algumas regras importantes. Essas regras determinam o que acontece com os recursos já implantados. A alteração solicitada e o tipo de artefato de recurso que está sendo implantado ou atualizado determinam quais ações são executadas.

- Atribuições de Funções
  - Se a função ou o destinatário da função (usuário, grupo ou aplicativo) for alterado, uma nova atribuição de função será criada. A atribuição de função já implantada permanece em vigor.
- Atribuições de Políticas
  - Se os parâmetros da atribuição de política forem alterados, a atribuição existente será atualizada.
  - Se a definição da atribuição de política for alterada, uma nova atribuição de política será criada.
    Atribuições de política implantadas anteriormente são deixadas no lugar.
  - Se o artefato de atribuição de política for removido do plano gráfico, implantado atribuições são deixadas no lugar de política.
- Modelos do Azure Resource Manager
  - O modelo é processado por meio do Resource Manager como **PUT**. Como cada tipo de recurso manipula isso de uma forma diferente, examine a documentação de cada recurso incluído para determinar o impacto dessa ação quando executada por blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização de atribuições

Ao atualizar atribuições, é possível fazer alterações que falham ao serem executadas. Um exemplo disso é alterar o local de um grupo de recursos depois que ele já foi implantado. É possível fazer qualquer alteração que seja compatível com o [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), mas qualquer alteração que resulte em erro por meio do Azure Resource Manager também resultará na falha da atribuição.

Não há nenhum limite para o número de vezes que uma atribuição pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização da atribuição.  Cenários de erro de exemplo:

- Parâmetro inválido
- Um objeto já existente
- Uma maudança não suportada pelo Resource Manager do Azure

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).