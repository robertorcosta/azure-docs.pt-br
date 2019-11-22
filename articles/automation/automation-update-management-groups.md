---
title: Usar grupos dinâmicos com o Azure Gerenciamento de Atualizações
description: Este artigo descreve como os grupos dinâmicos funcionam com a automação do Azure Gerenciamento de Atualizações.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 11/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 022c2061febb54666acee1cfed4ec595421660a3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278752"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usar grupos dinâmicos com o Gerenciamento de Atualizações

Gerenciamento de Atualizações fornece a capacidade de direcionar um grupo dinâmico de VMs do Azure ou não Azure para implantações de atualização. Esses grupos são avaliados no momento da implantação para que você não precise editar sua implantação para adicionar computadores.

## <a name="azure-machines"></a>Computadores do Azure

Esses grupos são definidos por uma consulta, quando uma implantação de atualização é iniciada, os membros desse grupo são avaliados. Grupos dinâmicos não funcionam com VMs clássicas. Ao definir sua consulta, os seguintes itens podem ser usados juntos para preencher o grupo dinâmico:

* Assinatura
* Grupos de recursos
* Locais
* Marcas

![Selecionar grupos](./media/automation-update-management/select-groups.png)

Para visualizar os resultados de um grupo dinâmico, clique no botão **Visualizar**. Esta pré-visualização mostra a associação ao grupo nesse momento, neste exemplo, estamos pesquisando máquinas com a tag **A função** é igual a **BackendServer**. Se mais computadores tiverem essa marca adicionada, eles serão adicionados a quaisquer implantações futuras em relação a esse grupo.

![grupos da visualização](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Computadores não Azure

Para computadores não Azure, as pesquisas salvas também conhecidas como grupos de computadores, são usadas para criar o grupo dinâmico. Para saber como criar uma pesquisa salva, consulte [criando um grupo de computadores](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Depois que o grupo for criado, você poderá selecioná-lo na lista de pesquisas salvas. Clique em **Visualizar** para visualizar os computadores na pesquisa salva naquele momento.

![Selecionar grupos](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Próximas etapas

Depois de criar um grupo dinâmico, você pode [criar uma implantação de atualização](automation-tutorial-update-management.md)
