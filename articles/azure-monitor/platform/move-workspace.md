---
title: Mova um espaço de trabalho do Log Analytics no Azure Monitor | Microsoft Docs
description: Saiba como mover seu espaço de trabalho do Log Analytics para outro grupo de assinatura ou recurso.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659485"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Mova um espaço de trabalho do Log Analytics para diferentes grupos de assinatura ou recursos

Neste artigo, você aprenderá as etapas para mover o espaço de trabalho do Log Analytics para outro grupo de recursos ou assinatura na mesma região. Você pode aprender mais sobre como mover recursos do Azure através do portal Azure, PowerShell, a CLI do Azure ou a API REST. no [Move recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> Você não pode mover um espaço de trabalho para uma região diferente.

## <a name="verify-active-directory-tenant"></a>Verificar inquilino do Diretório Ativo
As assinaturas de origem e destino do espaço de trabalho devem existir dentro do mesmo inquilino do Azure Active Directory. Use o Azure PowerShell para verificar se ambas as assinaturas têm o mesmo ID de inquilino.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Considerações sobre movimentação de espaço de trabalho
As soluções gerenciadas instaladas no espaço de trabalho serão movidas com a operação de movimentação do espaço de trabalho log analytics. Os agentes conectados permanecerão conectados e manterão o envio de dados para o espaço de trabalho após a mudança. Uma vez que a operação de movimentação exige que não haja nenhum link do espaço de trabalho para qualquer conta de automação, as soluções que dependem desse link devem ser removidas.

Soluções que devem ser removidas antes que você possa desvincular sua conta de automação:

- Gerenciamento de atualizações
- Controle de Alterações
- Iniciar/Parar VMs durante os horários inativos


### <a name="delete-in-azure-portal"></a>Exclusão no Portal do Azure
Use o seguinte procedimento para remover as soluções usando o portal Azure:

1. Abra o menu para o grupo de recursos em que todas as soluções estão instaladas.
2. Selecione as soluções para remover.
3. Clique **em Excluir recursos** e confirme os recursos a serem removidos clicando em **Excluir**.

![Excluir soluções](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Excluir usando o PowerShell

Para remover as soluções que usam o PowerShell, use o cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) como mostrado no exemplo a seguir:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>Remover regras de alerta
Para a solução **Start/Stop VMs,** você também precisa remover as regras de alerta criadas pela solução. Use o seguinte procedimento no portal Azure para remover essas regras.

1. Abra o menu **Monitor** e selecione **Alertas**.
2. Clique **em Gerenciar regras de alerta**.
3. Selecione as três seguintes regras de alerta e clique **em Excluir**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Excluir regras](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Desvincular a conta de automação
Use o procedimento a seguir para desvincular a conta automação do espaço de trabalho usando o portal Azure:

1. Abra o menu **de contas de automação** e selecione a conta a ser removida.
2. Na seção Recursos relacionados do menu, selecione **'Espaço de trabalho Vinculado '''Recursos'** **Related Resources** 
3. Clique **em Desvincular** o espaço de trabalho para desvincular o espaço de trabalho da sua conta de Automação.

    ![Desvincular o workspace](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Mova seu espaço de trabalho

### <a name="azure-portal"></a>Portal do Azure
Use o procedimento a seguir para mover seu espaço de trabalho usando o portal Azure:

1. Abra o menu **de espaços de trabalho do Log Analytics** e selecione seu espaço de trabalho.
2. Na **página Visão geral,** clique em **alterar** ao lado de **grupo de recursos** ou **assinatura**.
3. Uma nova página é aberta com uma lista de recursos relacionados ao espaço de trabalho. Selecione os recursos para mover para o mesmo grupo de assinatura de destino e recursos que o espaço de trabalho. 
4. Selecione um **grupo** **de assinatura** e recursos de destino . Se você estiver mudando o espaço de trabalho para outro grupo de recursos na mesma assinatura, você não verá a opção **Assinatura.**
5. Clique em **OK** para mover o espaço de trabalho e recursos selecionados.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Para mover seu espaço de trabalho usando o PowerShell, use o [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) como no exemplo a seguir:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Após a operação de movimentação, as soluções removidas e o link de conta de automação devem ser reconfigurados para trazer o espaço de trabalho de volta ao seu estado anterior.


## <a name="next-steps"></a>Próximas etapas
- Para obter uma lista de quais recursos de suporte a movimentação, consulte [O suporte à operação Mover para recursos](../../azure-resource-manager/management/move-support-resources.md).
