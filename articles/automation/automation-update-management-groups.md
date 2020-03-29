---
title: Use grupos dinâmicos com o Azure Update Management
description: Este artigo descreve como grupos dinâmicos trabalham com o Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420391"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usar grupos dinâmicos com o Gerenciamento de Atualizações

O Update Management fornece a capacidade de segmentar um grupo dinâmico de VMs Azure ou não-Azure para implantações de atualização. Esses grupos são avaliados no momento da implantação para que você não precise editar sua implantação para adicionar máquinas.

## <a name="azure-machines"></a>Máquinas azure

Esses grupos são definidos por uma consulta, quando uma implantação de atualização é iniciada, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com VMs clássicos. Ao definir sua consulta, os seguintes itens podem ser usados em conjunto para preencher o grupo dinâmico:

* Subscription
* Grupos de recursos
* Locais
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique no botão **Visualizar**. Esta pré-visualização mostra a associação ao grupo nesse momento, neste exemplo, estamos pesquisando máquinas com a tag **A função** é igual a **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras em relação a esse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Máquinas não-Azure

Para máquinas não-Azure, as pesquisas salvas também referidas como grupos de computador, são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [Criando um grupo de computador](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Uma vez que seu grupo é criado, você pode selecioná-lo na lista de pesquisas salvas. Clique **em Visualizar** os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um grupo dinâmico, você pode [criar uma implantação de atualização](automation-tutorial-update-management.md)
