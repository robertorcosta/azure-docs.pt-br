---
title: Usar grupos dinâmicos com a automação do Azure Gerenciamento de Atualizações
description: Este artigo descreve como os grupos dinâmicos funcionam com a automação do Azure Gerenciamento de Atualizações.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690769"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usar grupos dinâmicos com o Gerenciamento de Atualizações

Gerenciamento de Atualizações permite que você direcione um grupo dinâmico de VMs do Azure ou não Azure para implantações de atualização. O uso de um grupo dinâmico evita que você precise editar sua implantação para atualizar computadores.

> [!NOTE]
> Grupos dinâmicos não funcionam com VMs clássicas.

Você pode definir grupos dinâmicos para computadores Azure ou não Azure do **Gerenciamento de atualizações** no portal do Azure. Consulte [gerenciar atualizações para várias máquinas virtuais do Azure](manage-update-multi.md).

Um grupo dinâmico é definido por uma consulta que a automação do Azure avalia no momento da implantação. Mesmo se uma consulta de grupo dinâmico recuperar um grande número de máquinas, a automação do Azure poderá processar apenas um máximo de 1000 máquinas de cada vez. Veja [Assinatura do Azure e limites, cotas e restrições de serviço](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Se você espera atualizar mais de 1000 computadores, recomendamos que você divida as atualizações entre vários agendamentos de atualização. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definir grupos dinâmicos para computadores do Azure

Ao definir uma consulta de grupo dinâmico para computadores do Azure, você pode usar os seguintes itens para preencher o grupo dinâmico:

* Subscription
* Grupos de recursos
* Locais
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados da consulta de grupo dinâmico, clique em **Visualizar**. A visualização mostra a associação de grupo na hora atual. No exemplo, Estamos pesquisando computadores que têm a marca `Role` para o grupo **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras nesse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definir grupos dinâmicos para computadores não Azure

Um grupo dinâmico para computadores não Azure usa pesquisas salvas, também chamadas de grupos de computadores. Para saber como criar uma pesquisa salva, consulte [criando um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Depois que a pesquisa salva for criada, você poderá selecioná-la na lista de pesquisas salvas no **Gerenciamento de atualizações** no portal do Azure. Clique em **Visualizar** para visualizar os computadores na pesquisa salva.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um grupo dinâmico, você pode [criar uma implantação de atualização](automation-tutorial-update-management.md).
