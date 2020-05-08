---
title: Solucionando problemas da solução iniciar/parar VMs fora do horário comercial
description: Este artigo fornece informações sobre como solucionar problemas na solução iniciar/parar VM fora do horário de expediente.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855067"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Solucionar problemas da solução Iniciar/Parar VMs fora do horário comercial

Este artigo fornece informações sobre como solucionar problemas que surgem quando você trabalha com a solução iniciar/parar VMs do Azure em horários inativos.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação do Azure, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: a solução iniciar/parar VMs fora do horário comercial falhará para implantar corretamente

### <a name="issue"></a>Problema

Ao implantar a [solução iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md), você receberá um dos seguintes erros:

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

As implantações podem falhar devido a um dos seguintes motivos:

- Já existe uma conta de automação com o mesmo nome na região selecionada.
- Uma política não permite a implantação da solução iniciar/parar VMs fora do horário de expediente.
- O `Microsoft.OperationsManagement`tipo `Microsoft.Insights`de recurso `Microsoft.Automation` , ou não está registrado.
- Seu espaço de trabalho Log Analytics está bloqueado.
- Você tem uma versão desatualizada dos módulos AzureRM ou a solução iniciar/parar VMs fora do horário comercial.

### <a name="resolution"></a>Resolução

Examine as seguintes correções para obter possíveis soluções para o problema:

* As contas de automação precisam ser exclusivas em uma região do Azure, mesmo se estiverem em grupos de recursos diferentes. Verifique suas contas de automação existentes na região de destino.
* Uma política existente impede que um recurso necessário para a implantação iniciar/parar VMs fora do horário comercial seja implantado. Vá para as atribuições de política no portal do Azure e verifique se você tem uma atribuição de política que não permite a implantação desse recurso. Para saber mais, consulte [erro RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implantar a solução iniciar/parar VMs, sua assinatura precisa ser registrada nos seguintes namespaces de recursos do Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Para saber mais sobre erros ao registrar provedores, confira [resolver erros para o registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Se você tiver um bloqueio em seu espaço de trabalho do Log Analytics, vá até o seu espaço de trabalho no portal do Azure e remova quaisquer bloqueios no recurso.
* Se essas resoluções não resolverem o problema, siga as instruções em [atualizar a solução](../automation-solution-vm-management.md#update-the-solution) para reimplantar a solução iniciar/parar VMs fora do horário de expediente.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: falha ao iniciar ou parar todas as VMs

### <a name="issue"></a>Problema

Você configurou a solução iniciar/parar VMs fora do horário comercial, mas ela não inicia ou interrompe todas as VMs.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

- Uma agenda não está configurada corretamente.
- A conta Executar como pode não estar configurada corretamente.
- Um runbook pode ter se executado em erros.
- As VMs podem ter sido excluídas.

### <a name="resolution"></a>Resolução

Examine a lista a seguir para obter as possíveis soluções para o problema:

* Verifique se você configurou corretamente uma agenda para a solução iniciar/parar VMs fora do horário de expediente. Para saber como configurar uma agenda, consulte [agendas](../automation-schedules.md).

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. Procure trabalhos de um dos seguintes runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se sua [conta Executar como](../manage-runas-account.md) tem as permissões adequadas para as VMs que você está tentando iniciar ou parar. Para saber como verificar as permissões em um recurso, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure. Selecione **contas Executar como** em **configurações da conta**e selecione a conta Executar como apropriada.

* As VMs podem não ser iniciadas ou interrompidas se estiverem sendo explicitamente excluídas. As VMs excluídas são definidas `External_ExcludeVMNames` na variável na conta de automação para a qual a solução está implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: algumas das minhas VMs falham ao iniciar ou parar

### <a name="issue"></a>Problema

Você configurou a solução iniciar/parar VMs fora do horário comercial, mas ela não inicia ou interrompe algumas das VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

- No cenário de sequência, uma marca pode estar ausente ou incorreta.
- A VM pode ser excluída.
- A conta Executar como pode não ter permissões suficientes na VM.
- A VM pode ter um problema que o impediu de iniciar ou parar.

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter possíveis soluções para seu problema ou locais para procurar:

* Ao usar o [cenário de sequência](../automation-solution-vm-management.md) da solução iniciar/parar VMs fora do horário comercial, você deve verificar se cada VM que deseja iniciar ou parar tem a marca apropriada. Verifique se as VMs que você deseja iniciar têm a marca `sequencestart` e as VMs que você deseja parar têm a marca `sequencestop`. Ambas as marcas necessitam de um valor inteiro positivo. Você pode usar uma consulta semelhante ao exemplo a seguir para procurar todas as VMs com as marcas e seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* As VMs podem não ser iniciadas ou interrompidas se estiverem sendo explicitamente excluídas. As VMs excluídas são definidas `External_ExcludeVMNames` na variável na conta de automação para a qual a solução está implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar VMs, a conta Executar como da conta de automação deve ter as permissões apropriadas para a VM. Para saber como verificar as permissões em um recurso, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure. Selecione **contas Executar como** em **configurações da conta** e selecione a conta Executar como apropriada.
* Se a VM estiver tendo um problema iniciando ou desalocando, pode haver um problema na própria VM. Os exemplos são uma atualização que está sendo aplicada quando a VM está tentando desligar, um serviço que trava e muito mais. Vá para o recurso de VM e verifique **os logs de atividade** para ver se há erros nos logs. Você também pode tentar fazer logon na VM para ver se há erros nos logs de eventos. Para saber mais sobre como solucionar problemas de sua VM, consulte [Solucionando problemas de máquinas virtuais do Azure](../../virtual-machines/troubleshooting/index.yml).
* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. No portal, acesse sua conta de automação e selecione **trabalhos** em **automação de processo**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: meu runbook personalizado falha ao iniciar ou parar minhas VMs

### <a name="issue"></a>Problema

Você criou um runbook personalizado ou baixou um do Galeria do PowerShell e ele não está funcionando corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para a falha. Acesse sua conta de automação no portal do Azure e selecione **trabalhos** em **automação de processo**. Na página **Trabalhos**, procure por trabalhos do seu runbook para exibir as falhas de trabalho.

### <a name="resolution"></a>Resolução

É recomendável que você:

* Use a [solução iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md) para iniciar e parar VMs na automação do Azure. Essa solução foi criada pela Microsoft. 
* Lembre-se de que a Microsoft não dá suporte a runbooks personalizados. Você pode encontrar uma solução para o runbook personalizado da [solução de problemas de runbook](runbooks.md). Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: as VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

As VMs que você configurou na solução não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Esse problema é causado por marcação incorreta nas VMs.

### <a name="resolution"></a>Resolução

Siga estas etapas para garantir que a solução esteja configurada corretamente.

1. Verifique se todas as VMs que devem ser iniciadas ou paradas têm uma marca `sequencestart` ou `sequencestop`, dependendo da sua situação. Essas marcas precisam ter como valor um número inteiro positivo. As VMs são processadas em ordem crescente com base nesse valor.
1. Verifique se os grupos de recursos para as VMs que devem ser iniciadas ou paradas estão nas variáveis `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, dependendo da sua situação.
1. Teste as alterações executando o `SequencedStartStop_Parent` runbook com o `WHATIF` parâmetro definido como true para visualizar as alterações.

Para obter mais informações sobre como usar a solução para iniciar e parar VMs em sequência, consulte [iniciar/parar VMs em sequência](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: o trabalho iniciar/parar VMs fora do horário de expediente falha com o erro 403 Proibido

### <a name="issue"></a>Problema

Você encontra trabalhos que falharam com `403 forbidden` um erro para iniciar/parar VMs durante os runbooks da solução em horas inativas.

### <a name="cause"></a>Causa

Esse problema pode ser causado por uma conta Executar como configurada incorretamente ou expirada. Também pode ser devido a permissões inadequadas para os recursos da VM pela conta Executar como.

### <a name="resolution"></a>Resolução

Para verificar se a conta Executar como está configurada corretamente, vá para sua conta de automação no portal do Azure e selecione **contas Executar como** em **configurações da conta**. Se uma conta Executar como estiver incorretamente configurada ou expirada, o status mostrará a condição.

Se sua conta Executar como estiver configurada incorretamente, exclua e recrie sua conta Executar como. Para obter mais informações, consulte [gerenciar contas Executar como da automação do Azure](../manage-runas-account.md).

Se o certificado tiver expirado para sua conta Executar como, siga as etapas em [renovação de certificado autoassinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

Se houver permissões ausentes, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você deve fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure. Selecione **contas Executar como** em **configurações da conta**e selecione a conta Executar como apropriada.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Cenário: meu problema não está listado aqui

### <a name="issue"></a>Problema

Você enfrenta um problema ou resultado inesperado ao usar a solução iniciar/parar VMs fora do horário comercial que não está listada nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados ao usar uma versão antiga e desatualizada da solução.

> [!NOTE]
> A solução iniciar/parar VMs fora do horário comercial foi testada com os módulos do Azure que são importados para sua conta de automação quando você implanta a solução. Atualmente, a solução não funciona com versões mais recentes do módulo do Azure. Essa restrição afeta apenas a conta de automação que você usa para executar a solução iniciar/parar VMs fora do horário de expediente. Você ainda pode usar versões mais recentes do módulo do Azure em suas outras contas de automação, conforme descrito em [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

### <a name="resolution"></a>Resolução

Para resolver vários erros, remova e [atualize a solução iniciar/parar VMs fora do horário de expediente](../automation-solution-vm-management.md#update-the-solution). Você também pode verificar os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. 

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não puder resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente. O suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.