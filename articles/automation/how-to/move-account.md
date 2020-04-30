---
title: Mover sua conta de automação do Azure para outra assinatura
description: Este artigo descreve como mover sua conta de automação para outra assinatura.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681888"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Mover sua conta de automação do Azure para outra assinatura

A automação do Azure permite que você mova alguns recursos para um novo grupo de recursos ou assinatura. Você pode mover recursos por meio do portal do Azure, do PowerShell, do CLI do Azure ou da API REST. Para saber mais sobre o processo, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

A conta de automação do Azure é um dos recursos que você pode mover. Neste artigo, você aprenderá a mover as contas de automação para outro recurso ou assinatura. As etapas de alto nível para mover sua conta de automação são:

1. Remova suas soluções.
2. Desvincular seu espaço de trabalho.
3. Mova a conta de automação.
4. Exclua e recrie as contas Executar como.
5. Habilite novamente suas soluções.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="remove-solutions"></a>Remover soluções

Para desvincular seu espaço de trabalho da sua conta de automação, você deve remover essas soluções do seu espaço de trabalho:

- Controle de Alterações e Inventário
- Gerenciamento de atualizações
- Iniciar/Parar VMs nos horários inativos

1. No portal do Azure, encontre seu grupo de recursos.
2. Localize cada solução e clique em **excluir** na página excluir recursos.

    ![Excluir soluções do portal do Azure](../media/move-account/delete-solutions.png)

    Se preferir, você pode excluir as soluções usando o cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) :

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Remover regras de alerta para a solução iniciar/parar VMs fora do horário comercial

Para a solução iniciar/parar VMs fora do horário comercial, você também precisa remover as regras de alerta criadas pela solução.

1. Na portal do Azure, vá para o grupo de recursos e selecione **Monitoring** > **alertas** > de monitoramento**gerenciar regras de alerta**.

![Página de alertas mostrando a seleção de gerenciar regras de alerta](../media/move-account/alert-rules.png)

2. Na página regras, você verá uma lista dos alertas configurados nesse grupo de recursos. A solução cria estas regras:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecione as regras uma de cada vez e clique em **excluir** para removê-las.

    ![Página de regras solicitando confirmação de exclusão para regras selecionadas](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Se você não vir nenhuma regra de alerta na página regras, altere o campo **status** para desabilitado para mostrar alertas desabilitados, pois você pode tê-los desabilitados.

4. Quando as regras de alerta forem removidas, você deverá remover o grupo de ações criado para iniciar/parar VMs durante as horas de notificação de solução. No portal do Azure, selecione **monitorar** > **alertas** > **gerenciar grupos de ações**.

5. Selecione **StartStop_VM_Notification**. 

6. Na página grupo de ações, selecione **excluir**.

    ![Página grupo de ações](../media/move-account/delete-action-group.png)

    Se preferir, você pode excluir o grupo de ações usando o cmdlet [Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) :

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>Desvincular seu espaço de trabalho

Agora você pode desvincular seu espaço de trabalho:

1.  > No portal do Azure, selecione**recursos relacionados à** **conta** > de automação**espaço de trabalho vinculado**. 

2. Selecione **desvincular espaço de trabalho** para desvincular o espaço de trabalho da sua conta de automação.

    ![Desvincular um espaço de trabalho de uma conta de automação](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Mover sua conta de automação

Agora você pode mover sua conta de automação e seus runbooks. 

1. Na portal do Azure, navegue até o grupo de recursos da sua conta de automação. Selecione **mover** > **mover para outra assinatura**.

    ![Página grupo de recursos, mover para outra assinatura](../media/move-account/move-resources.png)

2. Selecione os recursos em seu grupo de recursos que você deseja mover. Certifique-se de incluir sua conta de automação, runbooks e Log Analytics recursos do espaço de trabalho.

## <a name="recreate-run-as-accounts"></a>Recriar contas Executar como

[As contas Executar como](../manage-runas-account.md) criam uma entidade de serviço no Azure Active Directory para autenticar com os recursos do Azure. Quando você altera as assinaturas, a conta de automação não usa mais a conta Executar como existente. Para recriar as contas Executar como:

1. Acesse sua conta de automação na nova assinatura e selecione **contas Executar como** em **configurações de conta**. Você verá que as contas Executar como são mostradas como incompletas agora.

    ![As contas Executar como estão incompletas](../media/move-account/run-as-accounts.png)

2. Exclua as contas Executar como uma por vez usando o botão **excluir** da página Propriedades. 

    > [!NOTE]
    > Se você não tiver permissões para criar ou exibir as contas Executar como, verá a seguinte mensagem: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` para saber mais sobre as permissões necessárias para configurar uma conta Executar como, consulte [permissões necessárias para configurar contas Executar como](../manage-runas-account.md#permissions).

3. Depois de excluir as contas Executar como, selecione **criar** na **conta Executar como do Azure**. 

4. Na página Adicionar conta Executar como do Azure, selecione **criar** para criar a conta Executar como e a entidade de serviço. 

5. Repita as etapas acima com a conta Executar como clássica do Azure.

## <a name="enable-solutions"></a>Habilitar soluções

Depois de recriar as contas Executar como, você deve reabilitar as soluções que você removeu antes da movimentação: 

1. Para ativar a solução de Controle de Alterações e inventário, selecione Controle de Alterações e inventário em sua conta de automação. Escolha o Log Analytics espaço de trabalho que você moveu e selecione **habilitar**.

2. Repita a etapa 1 para a solução Gerenciamento de Atualizações.

    ![Reabilitar soluções em sua conta de automação movida](../media/move-account/reenable-solutions.png)

3. Os computadores que estão integrados com suas soluções ficam visíveis quando você conecta o espaço de trabalho do Log Analytics existente. Para ativar a solução iniciar/parar VMs fora do horário comercial, você deve reimplantar a solução. Em **recursos relacionados**, selecione **iniciar/parar VMs** > **saiba mais sobre e habilite a solução** > **criar** para iniciar a implantação.

4. Na página Adicionar solução, escolha o espaço de trabalho Log Analytics e a conta de automação.

    ![Adicionar menu de solução](../media/move-account/add-solution-vm.png)

5. Configure a solução conforme descrito na [solução iniciar/parar VMs fora do horário comercial na automação do Azure](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>Verificar a movimentação

Quando a movimentação for concluída, verifique se os recursos listados abaixo estão habilitados. 

|Funcionalidade|Testes|Solução de problemas|
|---|---|---|
|Runbooks|Um runbook pode ser executado com êxito e se conectar aos recursos do Azure.|[Solucionar problemas de runbooks](../troubleshoot/runbooks.md)
|Controle do código-fonte|Você pode executar uma sincronização manual em seu repositório de controle do código-fonte.|[Integração de controle do código-fonte](../source-control-integration.md)|
|Controle de alterações e inventário|Verifique se você vê os dados de inventário atuais de seus computadores.|[Solucionar problemas de controle de alterações](../troubleshoot/change-tracking.md)|
|Gerenciamento de atualizações|Verifique se você vê seus computadores e se eles estão íntegros.</br>Executar uma implantação de atualização de software de teste.|[Solucionar problemas de gerenciamento de atualizações](../troubleshoot/update-management.md)|
|Recursos compartilhados|Verifique se você vê todos os seus recursos compartilhados, como [credenciais](../shared-resources/credentials.md), [variáveis](../shared-resources/variables.md)e similares.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como mover recursos no Azure, consulte [mover recursos no Azure](../../azure-resource-manager/management/move-support-resources.md).
