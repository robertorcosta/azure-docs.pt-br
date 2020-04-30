---
title: Usar grupos dinâmicos com a automação do Azure Gerenciamento de Atualizações
description: Este artigo descreve como os grupos dinâmicos funcionam com a automação do Azure Gerenciamento de Atualizações.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617434"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usar grupos dinâmicos com o Gerenciamento de Atualizações

Gerenciamento de Atualizações permite que você direcione um grupo dinâmico de VMs do Azure ou não Azure para implantações de atualização. Esses grupos, definidos por consultas, são avaliados no momento da implantação para que você não precise editar a implantação para adicionar computadores.

## <a name="azure-machines"></a>Computadores do Azure

Grupos dinâmicos não funcionam com VMs clássicas. Ao definir sua consulta, os seguintes itens podem ser usados juntos para preencher um grupo dinâmico:

* Assinatura
* Grupos de recursos
* Locais
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique em **Visualizar**. A visualização mostra a associação de grupo na hora atual. No exemplo, Estamos pesquisando computadores que têm a marca `Role` para o grupo **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras nesse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Computadores não Azure

Para computadores não Azure, as pesquisas salvas, também chamadas de grupos de computadores, são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [criando um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Depois que o grupo for criado, você poderá selecioná-lo na lista de pesquisas salvas. Clique em **Visualizar** para visualizar os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um grupo dinâmico, você pode [criar uma implantação de atualização](automation-tutorial-update-management.md).
