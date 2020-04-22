---
title: Mova sua conta do Azure Automation para outra assinatura
description: Este artigo descreve como mover sua conta de Automação para outra assinatura.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681888"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mova sua conta do Azure Automation para outra assinatura

O Azure Automation permite que você mova alguns recursos para um novo grupo de recursos ou assinatura. Você pode mover recursos através do portal Azure, PowerShell, a CLI do Azure ou a API REST. Para saber mais sobre o processo, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

A conta Azure Automation é um dos recursos que você pode mover. Neste artigo, você aprenderá a mover contas de Automação para outro recurso ou assinatura. As etapas de alto nível para mover sua conta de automação são:

1. Remova suas soluções.
2. Desvincule seu espaço de trabalho.
3. Mova a conta de Automação.
4. Exclua e recrie as contas Executar como.
5. Reative suas soluções.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Remover soluções

Para desvincular seu espaço de trabalho da sua conta de Automação, você deve remover essas soluções do seu espaço de trabalho:

- Controle de Alterações e Inventário
- Gerenciamento de atualizações
- Iniciar/Parar VMs nos horários inativos

1. No portal do Azure, encontre seu grupo de recursos.
2. Encontre cada solução e clique em **Excluir** na página Excluir recursos.

    ![Excluir soluções do portal Azure](../media/move-account/delete-solutions.png)

    Se preferir, você pode excluir as soluções usando o cmdlet [Remove-AzResource:](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0)

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Remova as regras de alerta para as VMs start/stop durante a solução de horas de folga

Para as VMs Start/Stop durante a solução de horas de folga, você também precisa remover as regras de alerta criadas pela solução.

1. No portal Azure, vá para o seu grupo de recursos e selecione**Alertas de** >  **monitoramento** > Gerenciar regras**de alerta**.

![Página alertas mostrando seleção de regras de gerenciamento de alerta](../media/move-account/alert-rules.png)

2. Na página Regras, você deve ver uma lista dos alertas configurados nesse grupo de recursos. A solução cria essas regras:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecione as regras uma de cada vez e clique em **Excluir** para removê-las.

    ![Página de regras solicitando confirmação de exclusão para regras selecionadas](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se você não ver nenhuma regra de alerta na página Regras, altere o campo **Status** para Desativado para exibir alertas desativados, porque você pode tê-los desabilitado.

4. Quando as regras de alerta forem removidas, você deve remover o grupo de ação criado para VMs Start/Stop durante as notificações de solução de horas de folga. No portal Azure, selecione **Monitor** > **Alertes** > Gerenciar grupos de**ação**.

5. Selecione **StartStop_VM_Notification**. 

6. Na página do grupo de ação, **selecione Excluir**.

    ![Página do grupo de ação](../media/move-account/delete-action-group.png)

    Se preferir, você pode excluir seu grupo de ação usando o cmdlet [Remove-AzActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0)

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Desvincule seu espaço de trabalho

Agora você pode desvincular seu espaço de trabalho:

1. No portal Azure, selecione **Automação conta** > **recursos** > relacionados**ao espaço de trabalho vinculado**. 

2. Selecione **Desvincular o espaço de trabalho** para desvincular o espaço de trabalho da sua conta de Automação.

    ![Desvincular um espaço de trabalho de uma conta de Automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mova sua conta de Automação

Agora você pode mover sua conta de Automação e seus livros de execução. 

1. No portal Azure, navegue até o grupo de recursos da sua conta de Automação. Selecione **Mover** > **mover para outra assinatura**.

    ![Página do grupo de recursos, mude para outra assinatura](../media/move-account/move-resources.png)

2. Selecione os recursos do seu grupo de recursos que deseja mover. Certifique-se de incluir sua conta de automação, runbooks e recursos do espaço de trabalho do Log Analytics.

## <a name="recreate-run-as-accounts"></a>Recriar executar como contas

[Executar como contas](../manage-runas-account.md) criar um principal de serviço no Azure Active Directory para autenticar com os recursos do Azure. Quando você altera assinaturas, a conta Automação não usa mais a conta Run As existente. Para recriar as contas executar como:

1. Vá para sua conta de automação na nova assinatura e **selecione Executar como contas** em **Configurações de conta**. Você verá que as contas do Run As mostram como incompletas agora.

    ![Executar como contas estão incompletas](../media/move-account/run-as-accounts.png)

2. Exclua as contas executar como uma de cada vez usando o botão **Excluir** na página Propriedades. 

    > [!NOTE]
    > Se você não tiver permissões para criar ou visualizar as contas `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Executar como, você verá a seguinte mensagem: Para saber sobre as permissões necessárias para configurar uma conta Executar como, consulte [Permissões necessárias para configurar contas execute como](../manage-runas-account.md#permissions).

3. Depois de excluir as contas Executar como, selecione **Criar** em **conta Azure Run As**. 

4. Na página Adicionar a conta Azure Run As, selecione **Criar** para criar a conta Executar como e o principal de serviço. 

5. Repita os passos acima com a conta Azure Classic Run As.

## <a name="enable-solutions"></a>Habilitar soluções

Depois de recriar as contas Executar como, você deve reativar as soluções que você removeu antes da mudança: 

1. Para ativar a solução De rastreamento e inventário de alterações, selecione Rastreamento de alterações e inventário em sua conta de automação. Escolha o espaço de trabalho do Log Analytics que você moveu e **selecione Habilitar**.

2. Repita a etapa 1 para a solução De gerenciamento de atualizações.

    ![Reativar soluções em sua conta de automação movida](../media/move-account/reenable-solutions.png)

3. As máquinas que estão a bordo de suas soluções são visíveis quando você conectado o espaço de trabalho log analytics existente. Para ativar as VMs Start/Stop durante a solução de horas de folga, você deve reimplantar a solução. Em **Recursos Relacionados,** selecione **Start/Stop VMs** > **Saiba mais sobre e habilite a solução** > **Criar** para iniciar a implantação.

4. Na página Adicionar solução, escolha o espaço de trabalho e a conta de automação do Log Analytics.

    ![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

5. Configure a solução conforme descrito nas [VMs Start/Stop durante a solução de horas de folga no Azure Automation](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verifique o movimento

Quando o movimento estiver concluído, verifique se os recursos listados abaixo estão ativados. 

|Recurso|Testes|Solução de problemas|
|---|---|---|
|Runbooks|Um runbook pode ser executado com sucesso e conectar-se aos recursos do Azure.|[Solucionar problemas de runbooks](../troubleshoot/runbooks.md)
|Controle do código-fonte|Você pode executar uma sincronização manual no repositório de controle de origem.|[Integração de controle do código-fonte](../source-control-integration.md)|
|Alterar rastreamento e inventário|Verifique se você vê os dados de inventário atuais de suas máquinas.|[Rastreamento de alterações de solução de problemas](../troubleshoot/change-tracking.md)|
|Gerenciamento de atualizações|Verifique se você vê suas máquinas e que elas são saudáveis.</br>Execute uma implantação de atualização de software de teste.|[Gerenciamento de atualização de solução de problemas](../troubleshoot/update-management.md)|
|Recursos compartilhados|Verifique se você vê todos os seus recursos [compartilhados,](../shared-resources/variables.md)como [credenciais,](../shared-resources/credentials.md)variáveis e afins.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a movimentação de recursos no Azure, consulte [Mover recursos no Azure](../../azure-resource-manager/management/move-support-resources.md).
