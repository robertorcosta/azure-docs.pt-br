---
title: Use grupos dinâmicos com o Gerenciamento de Atualização de Automação do Azure
description: Este artigo descreve como grupos dinâmicos trabalham com o Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617434"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usar grupos dinâmicos com o Gerenciamento de Atualizações

O Update Management permite que você direcione um grupo dinâmico de VMs Azure ou não-Azure para implantações de atualização. Esses grupos, definidos por consultas, são avaliados no momento da implantação para que você não precise editar sua implantação para adicionar máquinas.

## <a name="azure-machines"></a>Máquinas azure

Grupos dinâmicos não funcionam com VMs clássicos. Ao definir sua consulta, os seguintes itens podem ser usados em conjunto para preencher um grupo dinâmico:

* Subscription
* Grupos de recursos
* Locais
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique **em Visualizar**. A pré-visualização mostra a adesão do grupo no momento atual. No exemplo, estamos procurando por máquinas `Role` com a tag para o grupo **BackendServer**. Se mais máquinas tiverem essa tag adicionada, elas serão adicionadas a quaisquer implantações futuras contra esse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Máquinas não-Azure

Para máquinas não-Azure, as pesquisas salvas, também chamadas de grupos de computador, são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [Criando um grupo de computador](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Uma vez que seu grupo é criado, você pode selecioná-lo na lista de pesquisas salvas. Clique **em Visualizar** os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um grupo dinâmico, você pode [criar uma implantação de atualização](automation-tutorial-update-management.md).
