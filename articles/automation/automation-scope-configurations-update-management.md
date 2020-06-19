---
title: Trabalhar com configurações de escopo para o Gerenciamento de Atualizações da Automação do Azure
description: Este artigo mostra como trabalhar com as configurações de escopo quando você estiver usando Gerenciamento de Atualizações.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832020"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Trabalhar com configurações de escopo para Gerenciamento de Atualizações

Este artigo descreve como você pode trabalhar com configurações de escopo ao usar o recurso [Gerenciamento de Atualizações](automation-update-management.md) nas VMs. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Verificar a configuração de escopo

O Gerenciamento de Atualizações usa uma configuração de escopo dentro do workspace do Log Analytics para definir os computadores a serem habilitados para o recurso. A configuração de escopo é um grupo de uma ou mais pesquisas salvas utilizadas para limitar o escopo do recurso a computadores específicos. Para acessar as configurações de escopo:

1. Na conta de Automação, em **Recursos relacionados** selecione **Espaço de trabalho**. 

2. Selecione o espaço de trabalho em **Fonte de dados do espaço de trabalho** e selecione **Configurações de Escopo**.

3. Se o espaço de trabalho selecionado ainda não tiver o recurso Gerenciamento de Atualizações habilitado, ele criará a configuração de escopo de `MicrosoftDefaultScopeConfig-Updates`. 

4. Se o espaço de trabalho selecionado já tiver o recurso habilitado, ele não será implantado novamente e a configuração do escopo não será adicionada. 

5. Selecione as reticências em qualquer uma das configurações de escopo e clique em **Editar**. 

6. No painel Edição, selecione **Selecionar Grupos de Computadores**. O painel Grupos de Computadores mostra as pesquisas salvas que são usadas para criar a configuração de escopo.

## <a name="view-a-saved-search"></a>Exibir uma pesquisa salva

Quando um computador é adicionado ao Gerenciamento de Atualizações, ele também é adicionado a uma pesquisa salva no seu espaço de trabalho. A pesquisa salva é uma consulta que contém os computadores de destino.

1. Navegue para o espaço de trabalho do Log Analytics e selecione **Pesquisas salvas** em **Geral**. A pesquisa salva usada pelo Gerenciamento de Atualizações é:

|Nome     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

2. Selecione a pesquisa salva para visualizar a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

    ![Pesquisas salvas](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Próximas etapas

* Para trabalhar com o recurso, veja [Gerenciar atualizações e patches para as VMs do Azure](automation-tutorial-update-management.md).
* Para solucionar erros de recursos, veja [Solucionar problemas do Gerenciamento de Atualizações](troubleshoot/update-management.md).
* Para solucionar erros do agente de atualização do Windows, veja [Solucionar problemas do agente de atualização do Windows](troubleshoot/update-agent-issues.md).
* Para solucionar erros do agente de atualização do Linux, veja [Solucionar problemas do agente de atualização do Linux](troubleshoot/update-agent-issues-linux.md).