---
title: Excluir e recuperar o espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80395779"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Excluir e recuperar o espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de exclusão reversível do espaço de trabalho do Azure Log Analytics e como recuperar o espaço de trabalho excluído. 

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho

Quando você exclui um espaço de trabalho Log Analytics, uma operação de exclusão reversível é executada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, se a exclusão foi acidental ou intencional. Após o período de exclusão reversível, o recurso de espaço de trabalho e seus dados são não recuperáveis – seus dados são enfileirados para exclusão permanente e completamente limpos dentro de 30 dias. O nome do espaço de trabalho é ' liberado ' e você pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se você quiser substituir o comportamento de exclusão reversível e excluir seu espaço de trabalho permanentemente, siga as etapas em [excluir espaço de trabalho permanente](#permanent-workspace-delete).

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
> As soluções instaladas e os serviços vinculados, como sua conta de automação do Azure, são removidos permanentemente do espaço de trabalho no momento da exclusão e não podem ser recuperados. Eles devem ser reconfigurados após a operação de recuperação para colocar o espaço de trabalho em seu estado configurado anteriormente.

Você pode excluir um espaço de trabalho usando o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)ou na [portal do Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Para entrar, vá para a [portal do Azure](https://portal.azure.com). 
2. Na portal do Azure, selecione **todos os serviços**. Na lista de recursos, digite **log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **log Analytics espaços de trabalho**.
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho e clique em **excluir** na parte superior do painel central.
   ![Opção de exclusão do painel de propriedades do workspace](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quando a janela de mensagem de confirmação for exibida solicitando que você confirme a exclusão do workspace, clique em **Sim**.
   ![Confirmar exclusão do workspace](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Solução de problemas

Você deve ter permissões de ' Log Analytics colaborador ' para excluir Log Analytics espaço de trabalho.<br>
Se você receber uma mensagem de erro "*este nome de espaço de trabalho já está em uso*" ao criar um espaço de trabalho, poderia ser desde:
* O nome do espaço de trabalho não está disponível e sendo usado por alguém em sua organização ou por outro cliente.
* O espaço de trabalho foi excluído nos últimos 14 dias e seu nome permanece reservado para o período de exclusão reversível. Para substituir a exclusão reversível e excluir imediatamente seu espaço de trabalho e criar um novo espaço de trabalho com o mesmo nome, siga estas etapas para recuperar o espaço de trabalho primeiro e executar a exclusão permanente:<br>
   1. [Recupere](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) seu espaço de trabalho.
   2. [Exclua permanentemente](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) seu espaço de trabalho.
   3. Crie um novo espaço de trabalho usando o mesmo nome de espaço de trabalho.


## <a name="permanent-workspace-delete"></a>Exclusão de espaço de trabalho permanente
O método de exclusão reversível pode não se ajustar em alguns cenários, como desenvolvimento e teste, onde você precisa repetir uma implantação com as mesmas configurações e o nome do espaço de trabalho. Nesses casos, você pode excluir permanentemente seu espaço de trabalho e "substituir" o período de exclusão reversível. A operação de exclusão de espaço de trabalho permanente libera o nome do espaço de trabalho e você pode criar um novo espaço de trabalho usando o mesmo nome.


> [!IMPORTANT]
> Use a operação de exclusão de espaço de trabalho permanente com cuidado, pois sua irreversível e você não poderá recuperar seu espaço de trabalho e seus dados.

No momento, a exclusão de espaço de trabalho permanente pode ser executada via API REST.

> [!NOTE]
> Qualquer solicitação de API deve incluir um token de autorização de portador no cabeçalho da solicitação.
>
> Você pode adquirir o token usando:
> - [Registros de aplicativo](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navegue até portal do Azure usando o console do desenvolvedor (F12) no navegador. Examine uma das instâncias do **lote?** para a cadeia de caracteres de autenticação em **cabeçalhos de solicitação**. Isso estará na autorização de padrão *: portador <token> *. Copie e adicione isso à sua chamada à API, conforme mostrado nos exemplos.
> - Navegue até o site de documentação REST do Azure. Pressione **Experimente** em qualquer API, copie o token de portador e adicione-o à sua chamada à API.
Para excluir permanentemente seu espaço de trabalho, use os [espaços de trabalho – excluir]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) chamada à API REST com uma marca de força:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Onde ' eyJ0eXAiOiJKV1Qi... ' representa o token de autorização completo.

## <a name="recover-workspace"></a>Recuperar espaço de trabalho

Se você tiver permissões de colaborador para a assinatura e o grupo de recursos em que o espaço de trabalho foi associado antes da operação de exclusão reversível, poderá recuperá-lo durante seu período de exclusão reversível, incluindo seus dados, configuração e agentes conectados. Após o período de exclusão reversível, o espaço de trabalho é não recuperável e atribuído para exclusão permanente. Os nomes dos espaços de trabalho excluídos são preservados durante o período de exclusão reversível e não podem ser usados durante a tentativa de criar um novo espaço de trabalho.  

Você pode recuperar um espaço de trabalho recriando-o usando o seguinte espaço de trabalho criar métodos: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , contanto que as propriedades a seguir sejam preenchidas com os detalhes do espaço de trabalho excluído:

* ID da assinatura
* Nome do Grupo de Recursos
* Nome do workspace
* Região

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são colocados novamente após a operação de recuperação. Soluções e serviços vinculados foram removidos permanentemente do espaço de trabalho quando ele foi excluído e devem ser reconfigurados para colocar o espaço de trabalho no estado configurado anteriormente. Alguns dos dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e seus esquemas sejam adicionados ao espaço de trabalho.

> [!NOTE]
> * A recuperação do espaço de trabalho não tem suporte no [portal do Azure](https://portal.azure.com). 
> * Recriar um espaço de trabalho durante o período de exclusão reversível dá uma indicação de que esse nome de espaço de trabalho já está em uso. 
> 
