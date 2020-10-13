---
title: Excluir e recuperar um espaço de trabalho do Azure Log Analytics | Microsoft Docs
description: Saiba como excluir seu espaço de trabalho do Log Analytics se tiver criado um em uma assinatura pessoal ou para reestruturar seu modelo de espaço de trabalho.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 292e446d5b713a43f77ee5e579d7e6dd5905ff69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448534"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Excluir e recuperar um espaço de trabalho do Azure Log Analytics

Este artigo explica o conceito de exclusão reversível do espaço de trabalho do Azure Log Analytics e como recuperar o espaço de trabalho excluído.

## <a name="considerations-when-deleting-a-workspace"></a>Considerações ao excluir um espaço de trabalho

Quando você exclui um espaço de trabalho do Log Analytics, uma operação de exclusão reversível é executada para permitir a recuperação do espaço de trabalho, incluindo seus dados e agentes conectados dentro de 14 dias, seja a exclusão acidental ou intencional. Após o período de exclusão reversível, o recurso de espaço de trabalho e seus dados não são recuperáveis, ou seja, são enfileirados para exclusão permanente e completamente limpos dentro de 30 dias. O nome do espaço de trabalho é 'liberado' e você pode usá-lo para criar um novo espaço de trabalho.

> [!NOTE]
> Se você quiser substituir o comportamento de exclusão reversível e excluir permanentemente seu espaço de trabalho, siga as etapas em [excluir espaço de trabalho permanente](#permanent-workspace-delete).

Tenha cuidado ao excluir um espaço de trabalho, pois pode haver dados e configurações importantes que podem afetar negativamente a operação de serviço. Examine quais agentes, soluções e outros serviços do Azure armazenam seus dados em Log Analytics, como:

* Soluções de gerenciamento
* Automação do Azure
* Agentes em execução em máquinas virtuais do Windows e do Linux
* Agentes em execução em computadores com Windows e Linux em seu ambiente
* System Center Operations Manager

A operação de exclusão reversível exclui o recurso de espaço de trabalho e a permissão de qualquer usuário associado é interrompida. Se os usuários estiverem associados a outros espaço de trabalhos, eles poderão continuar usando o Log Analytics com esses outros espaço de trabalhos.

## <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

A operação de exclusão do espaço de trabalho remove o recurso do Resource Manager, mas sua configuração e dados são mantidos por 14 dias, dando a impressão de que o espaço de trabalho foi excluído. Todos os agentes e grupos de gerenciamento do System Center Operations Manager configurados para responder ao espaço de trabalho permanecem em um estado órfão durante o período de exclusão reversível. Além disso, o serviço fornece um mecanismo para recuperar o espaço de trabalho excluído, incluindo os dados e recursos conectados, basicamente desfazendo a exclusão.

> [!NOTE] 
> As soluções instaladas e os serviços vinculados, como a conta de Automação do Azure, são removidos permanentemente do espaço de trabalho no momento da exclusão e não podem ser recuperados. Eles devem ser reconfigurados após a operação de recuperação para trazer o espaço de trabalho ao seu estado configurado anteriormente.

Você pode excluir um espaço de trabalho usando o [PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), a [API REST](/rest/api/loganalytics/workspaces/delete) ou o [portal do Azure](https://portal.azure.com).

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**.
3. Na lista de espaço de trabalhos do Log Analytics, selecione um espaço de trabalho e clique em **Excluir** na parte superior do painel central.
4. Uma página de confirmação é exibida, mostrando a ingestão de dados no espaço de trabalho na última semana. Digite o nome do espaço de trabalho para confirmar e clique em **Excluir**.

   ![Confirmar exclusão do espaço de trabalho](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Exclusão permanente do espaço de trabalho
O método de exclusão reversível pode não se encaixar em alguns cenários, como desenvolvimento e teste, nos quais é necessário repetir uma implantação com as mesmas configurações e nome do espaço de trabalho. Nesses casos, você pode excluir permanentemente o espaço de trabalho e "substituir" o período de exclusão reversível. A operação de exclusão permanente libera o nome do espaço de trabalho, permitindo então que você crie um novo espaço de trabalho usando o mesmo nome.


> [!IMPORTANT]
> Tenha cuidado ao empregar a operação de exclusão permanente, pois ela é irreversível e você não poderá recuperar o espaço de trabalho e seus dados.

Adicione a marca '-ForceDelete ' para excluir permanentemente seu espaço de trabalho. A opção '-ForceDelete ' está disponível no momento com AZ. OperationalInsights 2.3.0 ou superior. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Recuperar o espaço de trabalho
Quando você exclui um espaço de trabalho do Log Analytics acidental ou intencionalmente, o serviço coloca o espaço de trabalho em um estado de exclusão reversível, tornando-o inacessível a qualquer operação. O nome do espaço de trabalho excluído é preservado durante o período de exclusão reversível e não pode ser usado para a criação de um novo espaço de trabalho. Após o período de exclusão reversível, o espaço de trabalho não é recuperável. Ele fica então programado para exclusão permanente e seu nome é liberado e pode ser usado na criação de um novo espaço de trabalho.

Você pode recuperar o espaço de trabalho durante o período de exclusão reversível, incluindo dados, configuração e agentes conectados. Você precisa ter permissões de colaborador para a assinatura e o grupo de recursos em que o espaço de trabalho estava localizado antes da operação de exclusão reversível. A recuperação do espaço de trabalho é realizada através da criação de um espaço de trabalho do Log Analytics com os dados do espaço de trabalho excluído, incluindo:

- ID da assinatura
- Nome do Grupo de Recursos
- Nome do workspace
- Região

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Escolha **workspaces do Log Analytics**. Você vê a lista de espaço de trabalhos no escopo selecionado.
3. Clique em **Recuperar** no menu superior esquerdo para abrir uma página com espaço de trabalhos no estado de exclusão reversível que podem ser recuperados.

   ![Captura de tela de Log Analytics área de trabalho em portal do Azure com Recover realçado na barra de menus.](media/delete-workspace/recover-menu.png)

4. Selecione o espaço de trabalho e clique em **Recuperar** para recuperá-lo.

   ![Captura de tela da caixa de diálogo recuperar Log Analytics espaços de trabalho excluídos em portal do Azure com um espaço de trabalho realçado e o botão recuperar selecionado.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

O espaço de trabalho e todos os seus dados são recuperados após a operação de recuperação. As soluções e os serviços vinculados foram removidos permanentemente do espaço de trabalho quando foram excluídos e devem ser reconfigurados para voltar o espaço de trabalho ao estado de configuração anterior. Alguns dados podem não estar disponíveis para consulta após a recuperação do espaço de trabalho até que as soluções associadas sejam reinstaladas e seus esquemas sejam adicionados.

> [!NOTE]
> * A recriação de um espaço de trabalho durante o período de exclusão reversível fornece uma indicação de que esse nome já está em uso. 
 
## <a name="troubleshooting"></a>Solução de problemas

Você deve ter, no mínimo, permissões de *Colaborador do Log Analytics* para excluir um espaço de trabalho.

* Se você não tiver certeza se o espaço de trabalho excluído está no estado de exclusão reversível e pode ser recuperado, clique em [recuperar](#recover-workspace) na página *log Analytics espaços de trabalho* para ver uma lista de espaços de trabalho excluídos por assinatura. Espaços de trabalho excluídos permanentemente não estão incluídos na lista.
* Se você receber uma mensagem de erro *Esse nome de espaço de trabalho já está em uso* ou *conflito* ao criar um espaço de trabalho, o motivo pode ser:
  * O nome do espaço de trabalho não está disponível e está sendo usado por alguém em sua organização ou por outro cliente.
  * O espaço de trabalho foi excluído durante os últimos 14 dias e seu nome mantido reservado para o período de exclusão reversível. Para substituir a exclusão reversível e excluir permanentemente o espaço de trabalho para criar um novo espaço de trabalho com o mesmo nome, siga estas etapas para recuperar o espaço de trabalho primeiro e executar a exclusão permanente:<br>
     1. [Recuperar](#recover-workspace) o espaço de trabalho.
     2. [Excluir permanentemente](#permanent-workspace-delete) o espaço de trabalho.
     3. Criar um novo espaço de trabalho usando o mesmo nome do espaço de trabalho.
