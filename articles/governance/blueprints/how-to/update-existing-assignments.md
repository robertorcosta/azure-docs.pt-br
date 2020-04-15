---
title: Atualize uma atribuição existente do portal
description: Conheça o mecanismo para atualizar uma atribuição de projeto existente no portal no Azure Blueprints.
ms.date: 04/15/2020
ms.topic: how-to
ms.openlocfilehash: a00a8bcc10b37af576777e3816a794225a3832f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381799"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de blueprint existente

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

## <a name="updating-assignments"></a>Atualizando atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Blueprints atribuídos** na página à esquerda.

1. Na lista de planos gráficos, clique em atribuição de planta. Em seguida, clique no botão **Atualizar atribuição** ou clique com o botão direito do mouse na atribuição do projeto e selecione **Atualizar a atribuição**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Atualize uma atribuição de projeto existente" border="false":::

1. A página **atribuir o projeto** será carregada pré-preenchida com todos os valores da atribuição original.
   Você pode alterar a **versão de definição do blueprint**, o estado **Bloquear Atribuição** e qualquer um dos parâmetros dinâmicos que existem na definição do blueprint. Clique em **Atribuir** quando terminar de fazer alterações.

1. Na página de detalhes de atribuição atualizada, veja o novo status. Neste exemplo, adicionamos **Bloqueio** à atribuição.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Atualizou uma atribuição de projeto existente - modo de bloqueio alterado" border="false":::

1. Explorar detalhes sobre outras **operações de atribuição** usando o drop-down. A tabela de atualizações de **recursos gerenciados** pela operação de atribuição selecionada.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Operações de atribuição de uma atribuição de projeto" border="false":::

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

Ao atualizar atribuições, é possível fazer alterações que falham ao serem executadas. Um exemplo disso é alterar o local de um grupo de recursos depois que ele já foi implantado. É possível fazer qualquer alteração que seja compatível com o [Azure Resource Manager](../../../azure-resource-manager/management/overview.md), mas qualquer alteração que resulte em erro por meio do Azure Resource Manager também resultará na falha da atribuição.

Não há nenhum limite para o número de vezes que uma atribuição pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização da atribuição.  Cenários de erro de exemplo:

- Parâmetro inválido
- Um objeto já existente
- Uma maudança não suportada pelo Resource Manager do Azure

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](../concepts/lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).