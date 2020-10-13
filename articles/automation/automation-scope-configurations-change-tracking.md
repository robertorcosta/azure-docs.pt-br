---
title: Limitar o Controle de Alterações de automação do Azure e o escopo de implantação de estoque
description: Este artigo informa como trabalhar com configurações de escopo para limitar o escopo de uma implantação de Controle de Alterações e de inventário.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185901"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limitar Controle de Alterações e escopo de implantação de estoque

Este artigo descreve como trabalhar com configurações de escopo ao usar o [controle de alterações e](change-tracking.md) o recurso de inventário para implantar alterações em suas VMs. Para obter mais informações, consulte [direcionando soluções de monitoramento em Azure monitor (versão prévia)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Sobre configurações de escopo

Uma configuração de escopo é um grupo de uma ou mais pesquisas salvas (consultas) usadas para limitar o escopo de Controle de Alterações e inventário a computadores específicos. A configuração de escopo é usada no espaço de trabalho Log Analytics para direcionar os computadores a serem habilitados. Quando você adiciona um computador a alterações do recurso, o computador também é adicionado a uma pesquisa salva no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de escopo

Para limitar o escopo para sua implantação de Controle de Alterações e inventário:

1. Em sua conta de automação, selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Clique em **ir para espaço de trabalho**.

3. Selecione **configurações de escopo (versão prévia)** em **fontes de dados do espaço de trabalho**.

4. Selecione as reticências à direita da  `MicrosoftDefaultScopeConfig-ChangeTracking` configuração de escopo e clique em **Editar**. 

5. No painel Edição, selecione **Selecionar Grupos de Computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo. A pesquisa salva usada pelo Controle de Alterações e pelo inventário é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. Selecione a pesquisa salva para exibir e editar a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

    ![Pesquisas salvas](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o Controle de Alterações e o Inventário, consulte [Gerenciar o Controle de Alterações e o Inventário](change-tracking-file-contents.md).
* Para solucionar problemas gerais de recurso, consulte [Solucionar problemas do Controle de Alterações e do Inventário](troubleshoot/change-tracking.md).
