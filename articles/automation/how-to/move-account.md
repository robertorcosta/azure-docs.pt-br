---
title: Mova sua conta do Azure Automation para outra assinatura
description: Este artigo descreve como mover sua conta de Automação para outra assinatura
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969839"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mova sua conta do Azure Automation para outra assinatura

O Azure fornece a capacidade de mover alguns recursos para um novo grupo de recursos ou assinatura. Você pode mover recursos através do portal Azure, PowerShell, a CLI do Azure ou a API REST. Para saber mais sobre o processo, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

As contas do Azure Automation são um dos recursos que podem ser movidos. Neste artigo, você aprenderá as etapas para mover contas de Automação para outro recurso ou assinatura.

As etapas de alto nível para mover sua conta de Automação são:

1. Remova suas soluções.
2. Desvincule seu espaço de trabalho.
3. Mova a conta de Automação.
4. Exclua e recrie as contas Executar como.
5. Reative suas soluções.

## <a name="remove-solutions"></a>Remover soluções

Para desvincular seu espaço de trabalho da sua conta de Automação, essas soluções devem ser removidas do seu espaço de trabalho:
- **Controle de Alterações e Inventário**
- **Gerenciamento de atualizações**
- **Iniciar/parar VMs durante o horário de folga**

Em seu grupo de recursos, encontre cada solução e **selecione Excluir**. Na **página Excluir recursos,** confirme os recursos a serem removidos e **selecione Excluir**.

![Excluir soluções do portal Azure](../media/move-account/delete-solutions.png)

Você pode realizar a mesma tarefa com o cmdlet [Remove-AzureRmResource,](/powershell/module/azurerm.resources/remove-azurermresource) conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Etapas adicionais para VMs start/stop

Para a solução **Start/Stop VMs,** você também precisa remover as regras de alerta criadas pela solução.

No portal Azure, vá para o seu grupo de recursos e selecione**Alertas de** >  **monitoramento** > Gerenciar regras**de alerta**.

![Página alertas mostrando seleção de regras de gerenciamento de alerta](../media/move-account/alert-rules.png)

Na página **Regras,** você deve ver uma lista dos alertas configurados nesse grupo de recursos. A solução **Start/Stop VMs** cria três regras de alerta:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Selecione essas três regras de alerta e, em seguida, **selecione Excluir**. Esta ação removerá essas regras de alerta.

![Página de regras solicitando confirmação de exclusão para regras selecionadas](../media/move-account/delete-rules.png)

> [!NOTE]
> Se você não ver nenhuma regra de alerta na página **Regras,** altere o **Status** para mostrar alertas **desativados,** porque você pode tê-los desabilitado.

Quando as regras de alerta forem removidas, remova o grupo de ação criado para as notificações da solução **Start/Stop VMs.**

No portal Azure, selecione **Monitor** > **Alertes** > Gerenciar grupos de**ação**.

Selecione **StartStop_VM_Notification** da lista. Na página do grupo de ação, **selecione Excluir**.

![Página do grupo de ação, selecione excluir](../media/move-account/delete-action-group.png)

Da mesma forma, você pode excluir seu grupo de ação usando o PowerShell com o cmdlet [Remove-AzureRmActionGroup,](/powershell/module/azurerm.insights/remove-azurermactiongroup) como visto no exemplo a seguir:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Desvincule seu espaço de trabalho

No portal Azure, selecione **Automação conta** > **recursos** > relacionados**ao espaço de trabalho vinculado**. Selecione **Desvincular o espaço de trabalho** para desvincular o espaço de trabalho da sua conta de Automação.

![Desvincular um espaço de trabalho de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mova sua conta de Automação

Depois de remover os itens anteriores, você pode continuar a remover sua conta de Automação e seus runbooks. No portal Azure, navegue até o grupo de recursos da sua conta de Automação. Selecione **Mover** > **mover para outra assinatura**.

![Página do grupo de recursos, mude para outra assinatura](../media/move-account/move-resources.png)

Selecione os recursos do seu grupo de recursos que deseja mover. Certifique-se de incluir os recursos **de automação,** **runbook**e **espaço de trabalho do Log Analytics.**

Depois que a mudança é concluída, há etapas adicionais necessárias para fazer tudo funcionar.

## <a name="re-create-run-as-accounts"></a>Recriar contas run as

[Executar como contas](../manage-runas-account.md) criar um principal de serviço no Azure Active Directory para autenticar com os recursos do Azure. Quando você altera assinaturas, a conta Automação não usa mais a conta Run As existente.

Vá para sua conta de automação na nova assinatura e **selecione Executar como contas** em **Configurações de conta**. Você verá que as contas do Run As mostram como incompletas agora.

![Executar como contas estão incompletas](../media/move-account/run-as-accounts.png)

Selecione cada conta Executar como. Na página **Propriedades,** **selecione Excluir** para excluir a conta Executar como.

> [!NOTE]
> Se você não tiver permissões para criar ou visualizar as contas Executar `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` como, você verá a seguinte mensagem: Para saber sobre as permissões necessárias para configurar uma conta Run As, consulte [Permissões necessárias para configurar contas run as](../manage-runas-account.md#permissions).

Depois que as contas Executar como são excluídas, selecione **Criar** em **conta Azure Run As**. Na página **Adicionar a conta Azure Run As,** selecione **Criar** para criar a conta Executar como e o principal de serviço. Repita as etapas anteriores com **a conta Azure Classic Run As**.

## <a name="enable-solutions"></a>Habilitar soluções

Depois de recriar as contas Executar as, você reativará as soluções removidas antes da mudança. Para ativar o **Rastreamento de alterações e o** gerenciamento de inventário e **atualização,** selecione o respectivo recurso em sua conta de Automação. Escolha o espaço de trabalho do Log Analytics que você moveu e **selecione Habilitar**.

![Reativar soluções em sua conta de automação movida](../media/move-account/reenable-solutions.png)

As máquinas que estão a bordo de suas soluções ficarão visíveis quando você conectado o espaço de trabalho do Log Analytics existente.

Para ativar as **VMs Start/Stop** durante a solução fora do horário de expediente, você precisará reimplantar a solução. Em **Recursos Relacionados,** selecione **Start/Stop VMs** > **Saiba mais sobre e habilite a solução** > **Criar** para iniciar a implantação.

Na página **Adicionar solução,** escolha sua conta de espaço de trabalho e automação do Log Analytics.

![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

Para obter instruções detalhadas sobre a configuração da solução, consulte [As VMs Start/Stop durante a solução off-hours no Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Verificação pós-mudança

Quando a movimentação estiver concluída, verifique a seguinte lista de tarefas que devem ser verificadas:

|Recurso|Testes|Link de solução de problemas|
|---|---|---|
|Runbooks|Um runbook pode ser executado com sucesso e conectar-se aos recursos do Azure.|[Solucionar problemas de runbooks](../troubleshoot/runbooks.md)
|Controle do código-fonte|Você pode executar uma sincronização manual no seu repo de controle de origem.|[Integração de controle do código-fonte](../source-control-integration.md)|
|Alterar rastreamento e inventário|Verifique se você vê os dados de inventário atuais de suas máquinas.|[Rastreamento de alterações de solução de problemas](../troubleshoot/change-tracking.md)|
|Gerenciamento de atualizações|Verifique se você vê suas máquinas e elas são saudáveis.</br>Execute uma implantação de atualização de software de teste.|[Gerenciamento de atualização de solução de problemas](../troubleshoot/update-management.md)|
|Recursos compartilhados|Verifique se você vê todos os seus recursos compartilhados, como [Credenciais,](../shared-resources/credentials.md) [Variáveis,](../shared-resources/variables.md)etc.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a movimentação de recursos no Azure, consulte [Mover recursos no Azure](../../azure-resource-manager/management/move-support-resources.md).
