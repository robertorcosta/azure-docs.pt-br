---
title: Limitar a automação do Azure Gerenciamento de Atualizações escopo de implantação
description: Este artigo informa como usar as configurações de escopo para limitar o escopo de uma implantação de Gerenciamento de Atualizações.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185884"
---
# <a name="limit-update-management-deployment-scope"></a>Limitar o escopo de implantação Gerenciamento de Atualizações

Este artigo descreve como trabalhar com configurações de escopo ao usar o recurso de [Gerenciamento de atualizações](automation-update-management.md) para implantar atualizações e patches em suas VMs. Para obter mais informações, consulte [direcionando soluções de monitoramento em Azure monitor (versão prévia)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Sobre configurações de escopo

Uma configuração de escopo é um grupo de uma ou mais pesquisas salvas (consultas) usadas para limitar o escopo de Gerenciamento de Atualizações a computadores específicos. A configuração de escopo é usada no espaço de trabalho Log Analytics para direcionar os computadores a serem habilitados. Quando você adiciona um computador para receber atualizações de Gerenciamento de Atualizações, o computador também é adicionado a uma pesquisa salva no espaço de trabalho.

## <a name="set-the-scope-limit"></a>Definir o limite de escopo

Para limitar o escopo para sua implantação de Gerenciamento de Atualizações:

1. Em sua conta de automação, selecione **espaço de trabalho vinculado** em **recursos relacionados**.

2. Clique em **ir para espaço de trabalho**.

3. Selecione **configurações de escopo (versão prévia)** em **fontes de dados do espaço de trabalho**.

4. Selecione as reticências à direita da `MicrosoftDefaultScopeConfig-Updates` configuração de escopo e clique em **Editar**. 

5. No painel de edição, expanda **Selecionar grupos de computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo. A pesquisa salva usada pelo Gerenciamento de Atualizações é:

    |Nome     |Categoria  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

6. Selecione a pesquisa salva para exibir e editar a consulta usada para popular o grupo. A imagem a seguir mostra a consulta e seus resultados:

    ![Pesquisas salvas](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o recurso, veja [Gerenciar atualizações e patches para as VMs do Azure](automation-tutorial-update-management.md).
* Para solucionar erros de recursos, veja [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar erros do agente de atualização do Windows, veja [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar erros do agente de atualização do Linux, veja [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).
