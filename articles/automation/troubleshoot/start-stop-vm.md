---
title: Solução de problemas Iniciando e Parando VMs - Automação Azure
description: Este artigo fornece informações sobre solução de problemas Iniciando e Parando VMs na Automação Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 73a9680cc570179c47b527a4844488da69193cb3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586092"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Solucionar problemas da solução Iniciar/Parar VMs fora do horário comercial

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: A solução Start/Stop VM falha em implantar corretamente

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

As implantações podem falhar devido a um dos seguintes motivos:

1. Já existe uma Conta de Automação com o mesmo nome na região selecionada.
2. Uma política está em vigor, que não permite a implantação da solução Iniciar/Parar VMs.
3. Os tipos de recurso `Microsoft.OperationsManagement`, `Microsoft.Insights`, ou `Microsoft.Automation` não estão registrados.
4. O seu espaço de trabalho do Log Analytics tem um bloqueio.
5. Você tem uma versão desatualizada dos módulos AzureRM ou da solução Start/Stop.

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter possíveis soluções para seu problema ou locais para procurar:

1. Contas de Automação precisam ser exclusivas em uma região do Azure, mesmo se elas estiverem em grupos de recursos diferentes. Verifique suas Contas de Automação existentes na região de destino.
2. Uma política existente impede que um recurso necessário para a solução Iniciar/Parar VM seja implantado. Acesse suas atribuições de política no portal do Azure e verifique se você tem uma atribuição de política que não permite a implantação deste recurso. Para saber mais sobre isso, confira [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Para implantar a solução Iniciar/Parar VM, sua assinatura precisa ser registrada nos seguintes namespaces de recursos do Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Confira [Resolver erros de registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md) para saber mais sobre os erros ao registrar provedores.
4. Se você tiver um bloqueio em seu espaço de trabalho do Log Analytics, vá até o seu espaço de trabalho no portal do Azure e remova quaisquer bloqueios no recurso.
5. Se as resoluções acima não resolverem seu problema, siga as instruções em [Atualizar a Solução](../automation-solution-vm-management.md#update-the-solution) para reimplantar a solução Start/Stop.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todas as VMs não conseguem iniciar/parar

### <a name="issue"></a>Problema

Você configurou a solução Iniciar/Parar VM, mas ela não inicia ou para todas as VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. Uma agenda não está configurada corretamente
2. Pode ser que a conta RunAs não esteja configurada corretamente
3. Pode ser que um runbook incorreu em erros
4. Pode ser que as VMs foram excluídas

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter possíveis soluções para seu problema ou locais para procurar:

* Verifique se você configurou corretamente a agenda para a solução de Iniciar/Parar VM. Para saber como configurar uma agenda, confira o artigo [Agendas](../automation-schedules.md).

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse a Conta de Automação e selecione **Trabalhos**, sob **Automação de Processos**. Na página **Trabalhos**, procure os trabalhos de um dos seguintes runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifique se sua [Conta RunAs](../manage-runas-account.md) tem as permissões adequadas para as VMs que você está tentando iniciar ou parar. Para saber como verificar as permissões em um recurso, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você precisará fornecer o ID do aplicativo para o principal de serviço usado pela Conta Run As. É possível recuperar esse valor, acesse sua Conta de Automação no portal do Azure, selecione **Contas Run as** em **Configurações de conta** e clique na Conta Run As apropriada.

* Você não pode iniciar ou parar as VMs caso elas estejam sendo excluídas explicitamente. As VMs excluídas do conjunto na variável **External_ExcludeVMNames** na Conta de Automação na qual a solução está implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Algumas das minhas VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Você configurou a solução de Iniciar/Parar VM, mas ela não inicia ou para algumas das VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. Se estiver usando o cenário de sequência, uma marca pode estar ausente ou incorreta
2. A VM pode ser excluída
3. A conta RunAs pode não ter permissões suficientes na VM
4. A VM pode ter algo que a impediu de iniciar ou parar

### <a name="resolution"></a>Resolução

Revise a lista a seguir para obter possíveis soluções para seu problema ou locais para procurar:

* Ao usar o [cenário de sequência](../automation-solution-vm-management.md) da solução Iniciar/Parar VMs fora do horário comercial, você deve verificar se cada VM que deseja iniciar ou parar tem a marca adequada. Verifique se as VMs que você deseja iniciar têm a marca `sequencestart` e as VMs que você deseja parar têm a marca `sequencestop`. Ambas as marcas necessitam de um valor inteiro positivo. Você pode usar uma consulta semelhante ao exemplo a seguir para procurar todas as VMs com as marcas e seus valores.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Você não pode iniciar ou parar as VMs caso elas estejam sendo excluídas explicitamente. As VMs excluídas do conjunto na variável **External_ExcludeVMNames** na Conta de Automação na qual a solução está implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar VMs, a conta RunAs da conta de Automação deve ter as permissões apropriadas para a VM. Para saber como verificar as permissões em um recurso, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você precisará fornecer o ID do aplicativo para o principal de serviço usado pela Conta Run As. É possível recuperar esse valor, acesse sua Conta de Automação no portal do Azure, selecione **Contas Run as** em **Configurações de conta** e clique na Conta Run As apropriada.

* Se a VM tem um problema na inicialização ou desalocação, esse comportamento pode ser causado por um problema na própria VM. Alguns exemplos ou problemas potenciais são: uma atualização está sendo aplicada durante a tentativa de desligamento, um serviço para de responder e muito mais. Navegue até o recurso da VM e verifique os **Logs de atividades** para ver se existem erros nos logs. Você também pode tentar fazer logon na VM para ver se existem erros nos Logs de eventos. Para saber mais sobre como solucionar problemas na VM, consulte [Máquinas virtuais azure de solução de problemas](../../virtual-machines/troubleshooting/index.yml)

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse a Conta de Automação e selecione **Trabalhos**, sob **Automação de Processos**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: Meu runbook personalizado falha em iniciar ou parar minhas VMs

### <a name="issue"></a>Problema

Você criou um runbook personalizado ou baixou um da Galeria do PowerShell e ele não está funcionando corretamente.

### <a name="cause"></a>Causa

A causa da falha pode ser uma entre diversas coisas. Acesse a Conta de Automação no portal do Azure e selecione **Trabalhos**, sob **Automação de Processos**. Na página **Trabalhos**, procure por trabalhos do seu runbook para exibir as falhas de trabalho.

### <a name="resolution"></a>Resolução

É recomendável usar a [solução Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md) para iniciar e parar VMs na Automação do Azure. Essa solução foi criada pela Microsoft. Não há suporte da Microsoft para runbooks personalizados. Você pode encontrar uma solução para seu runbook personalizado visitando o artigo [Solução de problemas do runbook](runbooks.md). Este artigo fornece diretrizes gerais e solução de problemas do runbooks de todos os tipos. Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse a Conta de Automação e selecione **Trabalhos**, sob **Automação de Processos**.

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: As VMs não começam ou param na sequência correta

### <a name="issue"></a>Problema

As VMs que você configurou na solução não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Isso é causado pela marcação incorreta nas VMs.

### <a name="resolution"></a>Resolução

Execute as etapas a seguir para garantir que a solução esteja configurada corretamente.

1. Verifique se todas as VMs que devem ser iniciadas ou paradas têm uma marca `sequencestart` ou `sequencestop`, dependendo da sua situação. Essas marcas precisam ter como valor um número inteiro positivo. As VMs são processadas em ordem crescente com base nesse valor.
2. Verifique se os grupos de recursos para as VMs que devem ser iniciadas ou paradas estão nas variáveis `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, dependendo da sua situação.
3. Teste suas alterações executando o runbook `SequencedStartStop_Parent` com o parâmetro WHATIF definido como True para visualizar as alterações.

Para obter instruções adicionais e mais detalhadas sobre como usar a solução para iniciar e parar VMs em sequência, confira [Iniciar/parar VMs em sequência](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Cenário: Trabalho de Start/Stop VM falha com 403 status proibido

### <a name="issue"></a>Problema

Você encontra trabalhos que falharam com um erro `403 forbidden` em runbooks da solução Iniciar/Parar VMs fora do horário comercial.

### <a name="cause"></a>Causa

Esse problema pode ser causado por uma conta Run As configurada incorretamente ou expirada. Ele também pode ser devido a permissões inadequadas para os recursos da VM pela Conta Run As das Contas de Automação.

### <a name="resolution"></a>Resolução

Para verificar se sua Conta Run As está configurada corretamente, acesse sua Conta de Automação no portal do Azure e selecione **Contas Run as** em **Configurações da conta**. Aqui você verá o status das Contas Run As, se uma Conta Run As estiver configurada incorretamente ou expirada o status mostrará isso.

Se sua conta Executar como estiver mal configurada, você deve excluir e recriar sua conta Executar como. Consulte [Gerenciar a execução de automação do Azure como contas](../manage-runas-account.md).

Se o certificado expirou para sua Conta Run As, siga as etapas listadas em [Renovação de certificado autoassinados](../manage-runas-account.md#cert-renewal) para renovar o certificado.

O problema pode ser causado por falta de permissões. Para saber como verificar as permissões em um recurso, consulte [Quickstart: Exibir funções atribuídas a um usuário usando o portal Azure](../../role-based-access-control/check-access.md). Você precisará fornecer o ID do aplicativo para o principal de serviço usado pela Conta Run As. É possível recuperar esse valor, acesse sua Conta de Automação no portal do Azure, selecione **Contas Run as** em **Configurações de conta** e clique na Conta Run As apropriada.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Cenário: Meu problema não está listado acima

### <a name="issue"></a>Problema

Você pode enfrentar um problema ou um resultado inesperado ao usar a solução Iniciar/Parar VMs fora do horário comercial que não está listada nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados ao usar uma versão antiga e desatualizada da solução.

> [!NOTE]
> As VMs Start/Stop durante a solução off-hours foram testadas com os módulos Azure que são importados para sua Conta de Automação quando você implanta a solução. A solução atualmente não funciona com versões mais recentes do módulo Azure. Isso só afeta a Conta de Automação que você usa para executar as VMs Start/Stop durante a solução fora do horário de expediente. Você ainda pode usar versões mais recentes do módulo Azure em suas outras contas de automação, conforme descrito em [Como atualizar módulos Do Azure PowerShell no Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Resolução

Para resolver diversos erros, é recomendável remover e atualizar a solução. Para saber como atualizar a solução, confira [Atualizar a solução Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md#update-the-solution). Além disso, você pode verificar os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse a Conta de Automação e selecione **Trabalhos**, sob **Automação de Processos**.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
