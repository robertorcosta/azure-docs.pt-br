---
title: Excluir e recuperar o espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395779"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Excluir e recuperar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de azure Log Analytics de exclusão suave do espaço de trabalho e como recuperar espaço de trabalho excluído. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho

Quando você exclui um espaço de trabalho do Log Analytics, uma operação de exclusão suave é realizada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, se a exclusão foi acidental ou intencional. Após o período de exclusão suave, o recurso do espaço de trabalho e seus dados não são recuperáveis – seus dados são enfileirados para exclusão permanente e completamente purgados dentro de 30 dias. O nome do espaço de trabalho é 'lançado' e você pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se você quiser anular o comportamento de exclusão suave e excluir seu espaço de trabalho permanentemente, siga as etapas em [Excluir espaço de trabalho permanente](#permanent-workspace-delete).

Você deseja ter cuidado ao excluir um espaço de trabalho porque pode haver dados e configurações importantes que podem afetar negativamente a operação do seu serviço. Revise quais agentes, soluções e outros serviços e fontes do Azure que armazenam seus dados no Log Analytics, tais como:

* Soluções de gerenciamento
* Automação do Azure
* Agentes em execução em máquinas virtuais do Windows e do Linux
* Agentes em execução em computadores com Windows e Linux em seu ambiente
* System Center Operations Manager

A operação soft-delete exclui o recurso do espaço de trabalho e a permissão de todos os usuários associados é quebrada. Se os usuários estiverem associados a outros espaços de trabalho, então eles podem continuar usando o Log Analytics com esses outros espaços de trabalho.

## <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

A operação de exclusão do espaço de trabalho remove o recurso do Gerenciador de recursos do espaço de trabalho, mas sua configuração e dados são mantidos por 14 dias, ao mesmo tempo em que dá a aparência de que o espaço de trabalho é excluído. Todos os agentes e grupos de gerenciamento do Gerenciador de Operações do Centro de Sistema configurados para reportar ao espaço de trabalho permanecem em estado órfão durante o período de exclusão suave. O serviço fornece ainda um mecanismo para recuperar o espaço de trabalho excluído, incluindo seus dados e recursos conectados, essencialmente desfazendo a exclusão.

> [!NOTE] 
> Soluções instaladas e serviços vinculados, como sua conta Azure Automation, são permanentemente removidos do espaço de trabalho no momento da exclusão e não podem ser recuperados. Estes devem ser reconfigurados após a operação de recuperação para trazer o espaço de trabalho para o seu estado previamente configurado.

Você pode excluir um espaço de trabalho usando [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou no [portal Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Para entrar, acesse o [portal Azure.](https://portal.azure.com) 
2. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, **digite Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **espaços de trabalho do Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **Excluir** na parte superior do painel médio.
   ![Opção de exclusão do painel de propriedades do workspace](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do workspace, clique em **Sim**.
   ![Confirmar exclusão do workspace](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Solução de problemas

Você deve ter permissões 'Log Analytics Contributor' para excluir o espaço de trabalho do Log Analytics.<br>
Se você receber uma mensagem de erro '*Este nome do espaço de trabalho já está em uso*' ao criar um espaço de trabalho, pode ser desde:
* O nome do espaço de trabalho não está disponível e está sendo usado por alguém da sua organização, ou por outro cliente.
* O espaço de trabalho foi excluído nos últimos 14 dias e seu nome mantido reservado para o período de exclusão suave. Para substituir o soft-delete e excluir imediatamente seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estas etapas para recuperar o espaço de trabalho primeiro e executar a exclusão permanente:<br>
   1. [Recupere](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) seu espaço de trabalho.
   2. [Exclua permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) seu espaço de trabalho.
   3. Crie um novo espaço de trabalho usando o mesmo nome do espaço de trabalho.


## <a name="permanent-workspace-delete"></a>Exclusão permanente do espaço de trabalho
O método soft-delete pode não se encaixar em alguns cenários, como desenvolvimento e testes, onde você precisa repetir uma implantação com as mesmas configurações e nome do espaço de trabalho. Nesses casos, você pode excluir permanentemente seu espaço de trabalho e "substituir" o período de exclusão suave. A operação de exclusão de espaço de trabalho permanente libera o nome do espaço de trabalho e você pode criar um novo espaço de trabalho usando o mesmo nome.


> [!IMPORTANT]
> Use a operação de exclusão permanente do espaço de trabalho com cautela, pois é irreversível e você não será capaz de recuperar seu espaço de trabalho e seus dados.

A exclusão permanente do espaço de trabalho pode ser realizada atualmente via API REST.

> [!NOTE]
> Qualquer solicitação de API deve incluir um token de autorização do Portador no cabeçalho de solicitação.
>
> Você pode adquirir o token usando:
> - [Registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navegue até o portal Azure usando o console do desenvolvedor (F12) no navegador. Procure em uma das instâncias do **lote** para a seqüência de autenticação em **Cabeçalhos de solicitação**. Isso estará na *autorização padrão: <token>Portador *. Copie e adicione isso à sua chamada de API, conforme mostrado nos exemplos.
> - Navegue até o site de documentação do Azure REST. Pressione **Experimente** em qualquer API, copie o token bearer e adicione-o à sua chamada de API.
Para excluir permanentemente seu espaço de trabalho, use a chamada [Workspaces - Exclua a]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) API REST com uma tag de força:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Onde 'eyJ0eXAiOiJKV1Qi...' representa o token de autorização total.

## <a name="recover-workspace"></a>Recuperar espaço de trabalho

Se você tiver permissões de Contribuinte para o grupo de assinatura e recursos onde o espaço de trabalho foi associado antes da operação de exclusão suave, você poderá recuperá-lo durante seu período de exclusão suave, incluindo seus dados, configuração e agentes conectados. Após o período de exclusão suave, o espaço de trabalho não é recuperável e atribuído para exclusão permanente. Os nomes dos espaços de trabalho excluídos são preservados durante o período de exclusão suave e não podem ser usados ao tentar criar um novo espaço de trabalho.  

Você pode recuperar um espaço de trabalho recriando-o usando os seguintes métodos de criação de espaço de trabalho: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [API REST,]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) desde que as seguintes propriedades sejam preenchidas com os detalhes do espaço de trabalho excluídos:

* ID da assinatura
* Nome do Grupo de Recursos
* Nome do workspace
* Região

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são trazidos de volta após a operação de recuperação. Soluções e serviços vinculados foram permanentemente removidos do espaço de trabalho quando foram excluídos e estes devem ser reconfigurados para trazer o espaço de trabalho para o seu estado previamente configurado. Alguns dos dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e seus esquemas sejam adicionados ao espaço de trabalho.

> [!NOTE]
> * A recuperação do espaço de trabalho não é suportada no [portal Azure](https://portal.azure.com). 
> * A recriação de um espaço de trabalho durante o período de exclusão suave dá uma indicação de que este nome do espaço de trabalho já está em uso. 
> 
