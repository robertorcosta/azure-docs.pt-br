---
title: Solução de problemas das VMs start/stop durante a solução de horas de folga
description: Este artigo fornece informações sobre a solução Start/Stop VM.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679157"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Solucionar problemas da solução Iniciar/Parar VMs fora do horário comercial

Este artigo fornece informações sobre problemas de solução de problemas que surgem durante o trabalho com VMs Start/Stop durante a solução de horas de folga.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: As VMs start/stop durante a solução de horas de folga não conseguem implantar corretamente

### <a name="issue"></a>Problema

Ao implantar [a solução Iniciar/Parar VMs fora do horário de trabalho](../automation-solution-vm-management.md), você recebe um dos seguintes erros:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Causa

As implantações podem falhar devido a uma das seguintes razões:

1. Já existe uma conta de Automação com o mesmo nome na região selecionada.
2. Uma diretiva está proibindo a implantação das VMs Start/Stop durante a solução de horas de folga.
3. O `Microsoft.OperationsManagement` `Microsoft.Insights`tipo `Microsoft.Automation` de recurso não está registrado.
4. Seu espaço de trabalho do Log Analytics está bloqueado.
5. Você tem uma versão desatualizada dos módulos AzureRM ou das VMs Start/Stop durante a solução de horas de folga.

### <a name="resolution"></a>Resolução

Revise as seguintes correções para soluções potenciais para o seu problema:

* Contas de Automação precisam ser exclusivas em uma região do Azure, mesmo se elas estiverem em grupos de recursos diferentes. Verifique suas contas de Automação existentes na região de destino.
* Uma diretiva existente impede que um recurso necessário para que as VMs Start/Stop durante a solução de horas de folga sejam implantadas. Acesse suas atribuições de política no portal do Azure e verifique se você tem uma atribuição de política que não permite a implantação deste recurso. Para saber mais sobre isso, confira [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implantar a solução Start/Stop VMs, sua assinatura precisa estar registrada nos seguintes espaços de nomes de recursos do Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Consulte [Resolver erros para registro de provedor esporu](../../azure-resource-manager/templates/error-register-resource-provider.md) para saber mais sobre erros ao registrar provedores.
* Se você tiver um bloqueio em seu espaço de trabalho do Log Analytics, vá até o seu espaço de trabalho no portal do Azure e remova quaisquer bloqueios no recurso.
* Se as resoluções acima não resolverem seu problema, siga as instruções em [Atualizar a Solução](../automation-solution-vm-management.md#update-the-solution) para reimplantar a solução Start/Stop.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todas as VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Você configurou as VMs Start/Stop durante a solução de horas de folga, mas não inicia ou para todas as VMs.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. Um horário não está configurado corretamente.
2. A conta Executar como pode não estar configurada corretamente.
3. Um runbook pode ter esbarrado em erros.
4. As VMs podem ter sido excluídas.

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter soluções potenciais para o seu problema:

* Verifique se você configurou corretamente um cronograma para as VMs Start/Stop durante a solução de horas de folga. Para saber como configurar uma agenda, confira o artigo [Agendas](../automation-schedules.md).

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. Procure empregos em um dos seguintes álbuns:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se sua [conta Executar as](../manage-runas-account.md) tem permissões adequadas para as VMs que você está tentando iniciar ou parar. Para saber como verificar as permissões em um recurso, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você precisará fornecer o ID do aplicativo para o principal de serviço usado pela conta Run As. Você pode recuperar esse valor indo para sua conta de Automação no portal do Azure, selecionando **Executar como contas** em **Configurações de conta**e clicando na conta apropriada Executar como.

* Você não pode iniciar ou parar as VMs caso elas estejam sendo excluídas explicitamente. As VMs excluídas `External_ExcludeVMNames` são definidas na variável na conta automação para a qual a solução é implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Algumas das minhas VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Você configurou as VMs Start/Stop durante a solução de horas de folga, mas não inicia ou interrompe algumas das VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. No cenário de seqüência, uma tag pode estar faltando ou incorreta.
2. O VM pode ser excluído.
3. A conta Run As pode não ter permissões suficientes na VM.
4. A VM pode ter um problema que o impediu de iniciar ou parar.

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter possíveis soluções para seu problema ou locais para procurar:

* Ao usar o cenário de [seqüência](../automation-solution-vm-management.md) das VMs Start/Stop durante a solução de horas de folga, você deve ter certeza de que cada VM que deseja iniciar ou parar tem a tag adequada. Verifique se as VMs que você deseja iniciar têm a marca `sequencestart` e as VMs que você deseja parar têm a marca `sequencestop`. Ambas as marcas necessitam de um valor inteiro positivo. Você pode usar uma consulta semelhante ao exemplo a seguir para procurar todas as VMs com as marcas e seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VMs podem não ser iniciados ou parados se eles estão sendo explicitamente excluídos. As VMs excluídas `External_ExcludeVMNames` são definidas na variável na conta automação para a qual a solução é implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar as VMs, a conta Run As para a conta Automação deve ter permissões apropriadas para a VM. Para saber como verificar as permissões em um recurso, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você precisará fornecer o ID do aplicativo para o principal de serviço usado pela conta Run As. Você pode recuperar esse valor indo para sua conta de Automação no portal do Azure, selecionando **Executar como contas** em **Configurações de conta** e clicando na conta 'Executar as' apropriada.

* Se a VM está tendo um problema de início ou negociação, pode haver um problema na própria VM. Por exemplo, uma atualização está sendo aplicada quando a VM está tentando desligar, um serviço trava e muito mais. Navegue até o recurso da VM e verifique os **Logs de atividades** para ver se existem erros nos logs. Você também pode tentar entrar na VM para ver se há algum erro nos registros de eventos. Para saber mais sobre como solucionar problemas na VM, consulte [Máquinas virtuais azure de solução de problemas](../../virtual-machines/troubleshooting/index.yml)

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, vá para sua conta de Automação e selecione **Empregos** em **Automação de Processos.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: Meu runbook personalizado falha em iniciar ou parar minhas VMs

### <a name="issue"></a>Problema

Você criou um runbook personalizado ou baixou um da Galeria do PowerShell e ele não está funcionando corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para o fracasso. Vá para sua conta de Automação no portal Azure e selecione **Empregos** em **Automação de Processos**. Na página Trabalhos, procure por trabalhos do seu runbook para exibir as falhas de trabalho.

### <a name="resolution"></a>Resolução

Recomenda-se:

* Use as [VMs Start/Stop durante](../automation-solution-vm-management.md) as horas de folga para iniciar e parar As VMs na Automação Azure. Essa solução foi criada pela Microsoft. 

* Esteja ciente de que a Microsoft não suporta runbooks personalizados. Você pode encontrar uma solução para o seu runbook personalizado a partir da solução de problemas do [runbook](runbooks.md). Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: As VMs não começam ou param na sequência correta

### <a name="issue"></a>Problema

As VMs que você configurou na solução não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Este problema é causado pela marcação incorreta nas VMs.

### <a name="resolution"></a>Resolução

Execute as etapas a seguir para garantir que a solução esteja configurada corretamente.

1. Verifique se todas as VMs que devem ser iniciadas ou paradas têm uma marca `sequencestart` ou `sequencestop`, dependendo da sua situação. Essas marcas precisam ter como valor um número inteiro positivo. As VMs são processadas em ordem crescente com base nesse valor.
2. Verifique se os grupos de recursos para as VMs que devem ser iniciadas ou paradas estão nas variáveis `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, dependendo da sua situação.
3. Teste suas alterações `SequencedStartStop_Parent` executando o `WHATIF` manual com o parâmetro definido como True para visualizar suas alterações.
4. Para obter mais informações sobre como usar a solução para iniciar e parar VMs em seqüência, consulte [Start/Stop VMs em seqüência](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: Start/Stop VMs durante o trabalho fora de hora falha com 403 erros proibidos

### <a name="issue"></a>Problema

Você encontra trabalhos `403 forbidden` que falharam com um erro para VMs Start/Stop durante os resumos de regras de solução de horas de folga.

### <a name="cause"></a>Causa

Esse problema pode ser causado por uma conta execute as mal configurada ou expirada. Também pode ser por causa de permissões inadequadas aos recursos da VM pela conta Run As.

### <a name="resolution"></a>Resolução

Para verificar se sua conta Executar as está configurada corretamente, vá para sua conta de Automação no portal Do Zure e **selecione Executar como contas** em **Configurações de conta**. Se uma conta Executar como estiver configurada ou expirada incorretamente, o status mostrará a condição.

Se sua conta Executar como estiver mal configurada, você deve excluir e recriar sua conta Executar como. Consulte [Gerenciar a execução de automação do Azure como contas](../manage-runas-account.md).

Se o certificado estiver vencido para sua conta Run As, consulte etapas na [renovação do certificado auto-assinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

Se houver permissões ausentes, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você deve fornecer o ID do aplicativo para o principal de serviço usado pela conta Run As. Você pode recuperar esse valor indo para sua conta de Automação no portal do Azure, selecionando **Executar como contas** em **Configurações de conta**e clicando na conta apropriada Executar como.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Cenário: Meu problema não está listado acima

### <a name="issue"></a>Problema

Você experimenta um problema ou um resultado inesperado ao usar as VMs Start/Stop durante a solução de horas de folga que não estão listadas nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados ao usar uma versão antiga e desatualizada da solução.

> [!NOTE]
> As VMs Start/Stop durante as horas de folga foram testadas com os módulos Azure que são importados para sua conta de Automação quando você implanta a solução. A solução atualmente não funciona com versões mais recentes do módulo Azure. Isso afeta apenas a conta de Automação que você usa para executar as VMs Start/Stop durante a solução de horas de folga. Você ainda pode usar versões mais recentes do módulo Azure em suas outras contas de Automação, conforme descrito em [Como atualizar módulos Do Azure PowerShell no Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Resolução

Para resolver muitos erros, recomenda-se remover e [atualizar as VMs Start/Stop durante a solução de horas de folga](../automation-solution-vm-management.md#update-the-solution). Além disso, você pode verificar os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. 

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.