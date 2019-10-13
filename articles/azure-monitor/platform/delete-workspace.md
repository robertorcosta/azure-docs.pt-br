---
title: Excluir e restaurar o espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: magoedte
ms.openlocfilehash: 4f03fc71a11c1ecb2e96b316efac9249395fc333
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285551"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Excluir e restaurar o espaço de trabalho do Azure Log Analytics
Este artigo explica o conceito de exclusão reversível do espaço de trabalho do Azure Log Analytics e como recuperar o espaço de trabalho excluído. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho
Quando você exclui um espaço de trabalho Log Analytics, uma operação de exclusão reversível é executada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, se a exclusão foi acidental ou intencional. Após o período de exclusão reversível, o espaço de trabalho e seus dados são não recuperáveis e são colocados na fila para exclusão permanente dentro de 30 dias.

Você deseja ter cuidado ao excluir um espaço de trabalho porque pode haver dados e configurações importantes que podem afetar negativamente a operação do serviço. Examine quais agentes, soluções e outros serviços e fontes do Azure que armazenam seus dados em Log Analytics, como:
* Soluções de gerenciamento
* Automação do Azure
* Agentes em execução em máquinas virtuais do Windows e do Linux
* Agentes em execução em computadores com Windows e Linux em seu ambiente
* System Center Operations Manager

A operação de exclusão reversível exclui o recurso de espaço de trabalho e a permissão de qualquer usuário associado é interrompida. Se os usuários estiverem associados a outros espaços de trabalho, eles poderão continuar usando Log Analytics com esses outros espaços de trabalho.

## <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível
A operação de exclusão do espaço de trabalho remove o recurso do Gerenciador de recursos de espaço de trabalho, mas sua configuração e seus dados são mantidos por 14 dias, enquanto dá a aparência de que o espaço de trabalho é excluído. Todos os agentes e grupos de gerenciamento de System Center Operations Manager configurados para relatar ao espaço de trabalho permanecem em um estado órfão durante o período de exclusão reversível. O serviço fornece ainda mais um mecanismo para recuperar o espaço de trabalho excluído, incluindo seus dados e recursos conectados, essencialmente desfazendo a exclusão.

> [!NOTE] 
> As soluções instaladas e os serviços vinculados, como a conta de automação, são removidos permanentemente do espaço de trabalho no momento da exclusão e não podem ser recuperados. Eles devem ser reconfigurados após a operação de recuperação para colocar o espaço de trabalho em sua funcionalidade anterior. 

Você pode excluir um espaço de trabalho usando o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou em [portal do Azure](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Excluir espaço de trabalho no portal do Azure
1. Para entrar, vá para a [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **excluir** na parte superior do painel central.
   ![Opção de exclusão do painel de propriedades do workspace](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do workspace, clique em **Sim**.
   ![Confirmar exclusão do workspace](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Recuperar espaço de trabalho
Se você tiver permissões de colaborador para a assinatura e o grupo de recursos em que o espaço de trabalho foi associado antes da operação de exclusão reversível, poderá recuperá-lo durante o período de exclusão reversível, incluindo seus dados, configuração e agentes conectados. Após o período de exclusão reversível, o espaço de trabalho é não recuperável e atribuído para exclusão permanente.

Você pode recuperar um espaço de trabalho recriando o espaço de trabalho usando o [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou o espaço de trabalho da [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) criar métodos, desde que essas propriedades sejam preenchidas com os detalhes do espaço de trabalho excluído, incluindo:
1.  ID da assinatura
2.  Nome do Grupo de Recursos
3.  Nome do workspace
4.  Região

> [!NOTE]
> * A recuperação do espaço de trabalho não tem suporte no [portal do Azure](https://portal.azure.com). Recriar um espaço de trabalho durante o período de exclusão reversível dá uma indicação de que esse nome de espaço de trabalho já está em uso.
> * Os nomes dos espaços de trabalho excluídos são preservados para o período de exclusão reversível e não podem ser usados durante a criação de um novo espaço de trabalho.
