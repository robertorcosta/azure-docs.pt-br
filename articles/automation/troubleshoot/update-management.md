---
title: Solucionando problemas de Gerenciamento de Atualizações de automação do Azure
description: Saiba como solucionar problemas e resolver questões com a solução Gerenciamento de Atualizações na automação do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678933"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>Solucionar problemas com a solução de Gerenciamento de Atualizações

Este artigo aborda os problemas que você pode encontrar ao usar a solução de Gerenciamento de Atualizações. Há uma solução de problemas de agente para o agente de Hybrid Runbook Worker para determinar o problema subjacente. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do Windows Update Agent](update-agent-issues.md) e [solucionar](update-agent-issues-linux.md)problemas do agente de atualização do Linux. Para outros problemas de integração, consulte [solução de problemas de integração de solução](onboarding.md).

>[!NOTE]
>Se você encontrar problemas ao integrar a solução em uma VM (máquina virtual), verifique o log de **Operations Manager** em **logs de aplicativos e serviços** no computador local. Procure eventos com a ID de evento 4502 e detalhes do evento `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`que contêm.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no seu Hybrid Runbook Worker, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Cenário: você recebe o erro "falha ao habilitar a solução de atualização"

### <a name="issue"></a>Problema

Ao tentar habilitar a solução Gerenciamento de Atualizações em sua conta de automação, você obtém o seguinte erro:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* Os requisitos de firewall de rede para o agente de Log Analytics podem não estar configurados corretamente. Essa situação pode fazer com que o agente falhe ao resolver as URLs de DNS.

* O direcionamento de solução está configurado incorretamente e o computador não está recebendo atualizações conforme o esperado.

* Você também pode observar que o computador mostra um status de `Non-compliant` em **conformidade**. Ao mesmo tempo, o **Agent Desktop Analytics** relata o agente como `Disconnected`.

### <a name="resolution"></a>Resolução

* Execute a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do sistema operacional.

* Vá para [planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas devem ser permitidos para que gerenciamento de atualizações funcionem.  

* Vá para [planejamento de rede](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) para saber mais sobre quais endereços e portas devem ser permitidos para que o agente de log Analytics funcione.

* Verifique se há problemas de configuração de escopo. A [configuração de escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais computadores são configurados para a solução. Se seu computador estiver aparecendo no seu espaço de trabalho, mas não no portal * * Gerenciamento de Atualizações, você precisará definir a configuração de escopo para os computadores de destino. Para saber mais sobre a configuração de escopo, confira [computadores integrados no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Remova a configuração de trabalho seguindo as etapas em [excluindo o Hybrid runbook Worker](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: atualização substituída indicada como ausente no Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Atualizações antigas são exibidas para uma conta de automação como ausente, embora tenham sido substituídas. Uma atualização substituída é aquela que você não precisa instalar porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. Gerenciamento de Atualizações ignora a atualização substituída e a torna não aplicável em favor da atualização substituta. Para obter informações sobre um problema relacionado, consulte a [atualização é substituída](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

As atualizações substituídas não são indicadas corretamente como recusadas para que elas possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização substituída se tornar 100% não aplicável, você deverá alterar o estado de aprovação dessa atualização `Declined`para o. Para alterar o estado de aprovação de todas as suas atualizações:

1. Na conta de automação, selecione **Gerenciamento de atualizações** para exibir o status da máquina. Consulte [Exibir avaliações de atualização](../manage-update-multi.md#view-an-update-assessment).

2. Verifique a atualização substituída para certificar-se de que é 100% não aplicável. 

3. Marque a atualização como recusada, a menos que você tenha uma pergunta sobre a atualização. 

4. Selecione **computadores** e, na coluna **conformidade** , Force uma nova verificação de conformidade. Consulte [gerenciar atualizações para vários computadores](../manage-update-multi.md).

5. Repita as etapas acima para outras atualizações substituídas.

6. Execute o assistente de limpeza para excluir arquivos das atualizações recusadas. 

7. Para Windows Server Update Services (WSUS), limpe manualmente todas as atualizações substituídas para atualizar a infraestrutura.

8. Repita esse procedimento regularmente para corrigir o problema de exibição e minimizar a quantidade de espaço em disco usada para o gerenciamento de atualizações.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Cenário: os computadores não aparecem no portal em Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Seus computadores têm os seguintes sintomas:

* Seu computador é `Not configured` exibido na exibição Gerenciamento de atualizações de uma VM.

* Seus computadores estão ausentes na exibição Gerenciamento de Atualizações da sua conta de automação do Azure.

* Você tem computadores que mostram como `Not assessed` sob **conformidade**. No entanto, você vê dados de pulsação em logs de Azure Monitor para o Hybrid Runbook Worker, mas não para Gerenciamento de Atualizações.

### <a name="cause"></a>Causa

Esse problema pode ser causado por problemas de configuração local ou pela configuração de escopo configurada incorretamente. Possíveis causas específicas:

* Talvez seja necessário registrar novamente e reinstalar o Hybrid Runbook Worker.

* Você pode ter definido uma cota em seu espaço de trabalho que foi atingido e que está impedindo o armazenamento de dados adicional.

### <a name="resolution"></a>Resolução

1. Execute a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do sistema operacional.

2. Certifique-se de que seu computador está relatando para o espaço de trabalho correto. Para obter orientação sobre como verificar esse aspecto, consulte [verificar a conectividade do agente para log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Verifique também se esse espaço de trabalho está vinculado à sua conta de automação do Azure. Para confirmar, acesse sua conta de automação e selecione **espaço de trabalho vinculado** em **recursos relacionados**.

3. Verifique se as máquinas aparecem no espaço de trabalho Log Analytics vinculado à sua conta de automação. Execute a consulta a seguir no espaço de trabalho Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente. Provavelmente, há um problema de configuração local e você deve [reinstalar o agente](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). 

5. Se o computador aparecer nos resultados da consulta, verifique se há problemas de configuração de escopo. A [configuração de escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais computadores estão configurados para a solução. 

6. Se o seu computador estiver aparecendo no seu espaço de trabalho, mas não no Gerenciamento de Atualizações, você deverá configurar a configuração de escopo para o computador de destino. Para saber como fazer isso, confira [carregar computadores no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. Em seu espaço de trabalho, execute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se você obtiver `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` um resultado, a cota definida em seu espaço de trabalho foi atingida, o que interrompeu os dados de serem salvos. Em seu espaço de trabalho, vá para **Gerenciamento de volume de dados** em **uso e custos estimados**e altere ou remova a cota.

9. Se o problema ainda não estiver resolvido, siga as etapas em [implantar um Hybrid runbook Worker do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para Windows. Para o Linux, siga as etapas em [implantar um Hybrid runbook Worker do Linux](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: não é possível registrar o provedor de recursos de automação para assinaturas

### <a name="issue"></a>Problema

Quando você trabalha com soluções em sua conta de automação, ocorre o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

O provedor de recursos de automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o provedor de recursos de automação, siga estas etapas na portal do Azure.

1. Na lista de serviços do Azure, na parte inferior do portal, selecione **todos os serviços**e, em seguida, selecione **assinaturas** no grupo de serviços geral.

2. Selecione sua assinatura.

3. Em **configurações**, selecione **provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos Microsoft. Automation está registrado.

5. Se não estiver listado, registre o provedor Microsoft. Automation seguindo as etapas em [resolver erros para o registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Cenário: a atualização agendada com um agendamento dinâmico perdeu alguns computadores

### <a name="issue"></a>Problema

As máquinas incluídas em uma visualização de atualização nem todas aparecem na lista de máquinas corrigidas durante uma execução agendada.

### <a name="cause"></a>Causa

Esse problema pode ter uma das seguintes causas:

* As assinaturas definidas no escopo em uma consulta dinâmica não estão configuradas para o provedor de recursos de automação registrado.

* Os computadores não estavam disponíveis ou não tinham marcas apropriadas quando a agenda foi executada.

### <a name="resolution"></a>Resolução

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Assinaturas não configuradas para o provedor de recursos de automação registrado

Se sua assinatura não estiver configurada para o provedor de recursos de automação, você não poderá consultar nem buscar informações sobre computadores nessa assinatura. Use as etapas a seguir para verificar o registro da assinatura.

1. No [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal), acesse a lista de serviços do Azure.

2. Selecione **todos os serviços**e, em seguida, selecione **assinaturas** no grupo de serviços geral. 

3. Localize a assinatura definida no escopo da sua implantação.

4. Em **configurações**, escolha **provedores de recursos**.

5. Verifique se o provedor de recursos Microsoft. Automation está registrado.

6. Se não estiver listado, registre o provedor Microsoft. Automation seguindo as etapas em [resolver erros para o registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Computadores não disponíveis ou não marcados corretamente quando a agenda foi executada

Use o procedimento a seguir se sua assinatura estiver configurada para o provedor de recursos de automação, mas a execução da agenda de atualização com os [grupos dinâmicos](../automation-update-management-groups.md) especificados perdeu alguns computadores.

1. No portal do Azure, abra a conta de automação e selecione **Gerenciamento de atualizações**.

2. Verifique [Gerenciamento de atualizações histórico](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) para determinar a hora exata em que a implantação da atualização foi executada. 

3. Para computadores que você suspeita de ter sido perdido por Gerenciamento de Atualizações, use o grafo de recursos do Azure (ARG) para [localizar as alterações na máquina](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Procure alterações em um período considerável, como um dia, antes da execução da implantação de atualização.

5. Verifique os resultados da pesquisa para quaisquer alterações do sistema, como excluir ou atualizar alterações, para os computadores neste período. Essas alterações podem alterar o status ou as marcas da máquina para que os computadores não sejam selecionados na lista de computadores quando as atualizações forem implantadas.

6. Ajuste as configurações de máquinas e recursos conforme necessário para corrigir problemas de status de máquina ou marca.

7. Execute novamente o agendamento de atualização para garantir que a implantação com os grupos dinâmicos especificados inclua todos os computadores.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Cenário: os computadores esperados não aparecem na visualização para o grupo dinâmico

### <a name="issue"></a>Problema

As máquinas virtuais para escopos selecionados de um grupo dinâmico não estão aparecendo na lista de portal do Azure visualização. Essa lista consiste em todos os computadores recuperados por uma consulta de ARG para os escopos selecionados. Os escopos são filtrados para computadores que têm Hybrid runbook Workers instalados e para os quais você tem permissões de acesso. 

### <a name="cause"></a>Causa
 
Estas são as possíveis causas para esse problema:

* Você não tem o acesso correto nos escopos selecionados.
* A consulta de ARG não recupera os computadores esperados.
* O Hybrid Runbook Worker não está instalado nos computadores.

### <a name="resolution"></a>Resolução 

#### <a name="incorrect-access-on-selected-scopes"></a>Acesso incorreto nos escopos selecionados

O portal do Azure só exibe computadores para os quais você tem acesso de gravação em um determinado escopo. Se você não tiver o acesso correto para um escopo, consulte [tutorial: conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>A consulta de ARG não retorna computadores esperados

Siga as etapas abaixo para descobrir se suas consultas estão funcionando corretamente.

1. Execute uma consulta ARG formatada conforme mostrado abaixo na folha Gerenciador de grafo de recursos em portal do Azure. Essa consulta imita os filtros que você selecionou quando criou o grupo dinâmico no Gerenciamento de Atualizações. Consulte [usar grupos dinâmicos com gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Veja um exemplo:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```
 
2. Verifique se os computadores que você está procurando estão listados nos resultados da consulta. 

3. Se os computadores não estiverem listados, provavelmente há um problema com o filtro selecionado no grupo dinâmico. Ajuste a configuração do grupo conforme necessário.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker não instalado nos computadores

As máquinas aparecem nos resultados da consulta de ARG, mas ainda não aparecem na visualização do grupo dinâmico. Nesse caso, as máquinas podem não ser designadas como Hybrid Workers e, portanto, não podem executar a automação do Azure e trabalhos de Gerenciamento de Atualizações. Para garantir que as máquinas que você está esperando ver estejam configuradas como Hybrid runbook workers:

1. Na portal do Azure, acesse a conta de automação de um computador que não está aparecendo corretamente.

2. Selecione **grupos de trabalho híbrido** em **automação de processo**.

3. Selecione a guia **grupos de trabalho híbrido do sistema** .

4. Valide se o Hybrid Worker está presente para esse computador.

5. Se o computador não estiver configurado como um trabalhador híbrido, faça ajustes usando as instruções em [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Ingresse o computador no grupo de Hybrid Runbook Worker.

7. Repita as etapas acima para todos os computadores que não foram exibidos na visualização.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Cenário: componentes para a solução Gerenciamento de Atualizações habilitada, enquanto a VM continua a ser mostrada como sendo configurada

### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* A comunicação com a conta de automação está sendo bloqueada.

* Há um nome de computador duplicado com IDs de computador de origem diferentes. Esse cenário ocorre quando uma VM com um nome de computador específico é criada em grupos de recursos diferentes e está se comunicando com o mesmo espaço de trabalho do agente de logística na assinatura.

* A imagem da VM que está sendo integrada pode vir de uma máquina clonada que não foi preparada com o Sysprep (preparação do sistema) com o agente do Log Analytics para Windows instalado.

### <a name="resolution"></a>Resolução

Para ajudar a determinar o problema exato com a VM, execute a consulta a seguir no espaço de trabalho Log Analytics que está vinculado à sua conta de automação.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicação com a conta de automação bloqueada

Vá para [planejamento de rede](../automation-update-management.md#ports) para saber mais sobre quais endereços e portas devem ser permitidos para que gerenciamento de atualizações funcionem.

#### <a name="duplicate-computer-name"></a>Nome de computador duplicado

Renomeie suas VMs para garantir nomes exclusivos em seu ambiente.

#### <a name="onboarded-image-from-cloned-machine"></a>Imagem integrada do computador clonado

Se você estiver usando uma imagem clonada, diferentes nomes de computador terão a mesma ID de computador de origem. Nesse caso:

1. No espaço de trabalho Log Analytics, remova a VM da pesquisa salva para a `MicrosoftDefaultScopeConfig-Updates` configuração de escopo se ela for mostrada. As pesquisas salvas podem ser encontradas em **Geral** no workspace.

2. Execute o cmdlet a seguir.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Execute `Restart-Service HealthService` para reiniciar o serviço de integridade. Esta operação recria a chave e gera um novo UUID.

4. Se essa abordagem não funcionar, execute o Sysprep na imagem primeiro e, em seguida, instale o MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Cenário: você recebe um erro de assinatura vinculada quando cria uma implantação de atualização para computadores em outro locatário do Azure

### <a name="issue"></a>Problema

Você encontrará o seguinte erro ao tentar criar uma implantação de atualização para computadores em outro locatário do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem VMs do Azure em outro locatário que está incluído em uma implantação de atualização.

### <a name="resolution"></a>Resolução

Use a solução alternativa a seguir para obter esses itens agendados. Você pode usar o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro para criar uma agenda. Em seguida, use o cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passe os computadores no outro locatário para o `NonAzureComputer` parâmetro. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Cenário: reinicializações não explicadas

### <a name="issue"></a>Problema

Embora você tenha definido a opção de **controle de reinicialização** para **nunca reinicializar**, as máquinas ainda são reinicializadas após a instalação das atualizações.

### <a name="cause"></a>Causa

Windows Update pode ser modificado por várias chaves do registro, qualquer uma das quais pode modificar o comportamento de reinicialização.

### <a name="resolution"></a>Resolução

Examine as chaves do registro listadas em [Configurando atualizações automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [as chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) para garantir que seus computadores estejam configurados corretamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Cenário: o computador mostra "falha ao iniciar" em uma implantação de atualização

### <a name="issue"></a>Problema

Um computador mostra um `Failed to start` status. Ao exibir os detalhes específicos do computador, você verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Esse problema pode ocorrer por um dos seguintes motivos:

* O computador não existe mais.
* A máquina está desligada e inacessível.
* O computador tem um problema de conectividade de rede e, portanto, o Hybrid Worker no computador está inacessível.
* Houve uma atualização para o MMA que alterou a ID do computador de origem.
* A execução de atualização foi limitada se você atingiu o limite de 2000 trabalhos simultâneos em uma conta de automação. Cada implantação é considerada um trabalho e cada computador em uma implantação de atualização conta como um trabalho. Qualquer outro trabalho de automação ou implantação de atualização em execução no momento em sua conta de automação conta para o limite de trabalhos simultâneos.

### <a name="resolution"></a>Resolução

Quando aplicável, use [grupos dinâmicos](../automation-update-management-groups.md) para suas implantações de atualização. Além disso, você pode executar as etapas a seguir.

1. Verifique se o computador ainda existe e acessível. 
2. Se o computador não existir, edite a implantação e remova o computador.
3. Consulte a seção [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários para gerenciamento de atualizações e, em seguida, verifique se o computador atende a esses requisitos.
4. Verifique a conectividade com o Hybrid Runbook Worker usando a solução de problemas do agente de Hybrid Runbook Worker. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md).
5. Execute a consulta a seguir em Log Analytics para localizar computadores em seu ambiente para os quais a ID do computador de origem foi alterada. Procure computadores que tenham o mesmo `Computer` valor, mas um valor `SourceComputerId` diferente.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Depois de encontrar as máquinas afetadas, edite as implantações de atualização direcionadas a essas máquinas e, em seguida `SourceComputerId` , remova-as e as Leia para que reflitam o valor correto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Cenário: as atualizações são instaladas sem uma implantação

### <a name="issue"></a>Problema

Quando você registra um computador Windows no Gerenciamento de Atualizações, você vê as atualizações instaladas sem uma implantação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estiverem disponíveis. Esse comportamento pode causar confusão se você não agendou uma atualização para ser implantada no computador.

### <a name="resolution"></a>Resolução

A `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` chave do registro usa como padrão uma configuração de 4 `auto download and install`:.

Para clientes Gerenciamento de Atualizações, é recomendável definir essa chave como 3 `auto download but do not auto install`:.

Para obter mais informações, consulte [Configurando atualizações automáticas](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: a máquina já está registrada em uma conta diferente

### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

O computador já foi integrado a outro espaço de trabalho para Gerenciamento de Atualizações.

### <a name="resolution"></a>Resolução

1. Siga as etapas em [computadores que não aparecem no portal em gerenciamento de atualizações](#nologs) para certificar-se de que a máquina está relatando para o espaço de trabalho correto.
2. Limpe os artefatos no computador [excluindo o grupo de runbooks híbrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Cenário: a máquina não pode se comunicar com o serviço

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Um proxy, gateway ou firewall pode estar bloqueando A comunicação de rede. 

### <a name="resolution"></a>Resolução

Examine sua rede e verifique se as portas e os endereços apropriados são permitidos. Consulte [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning) para obter uma lista de portas e endereços que são necessários para gerenciamento de atualizações e Hybrid runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Cenário: não é possível criar certificado autoassinado

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Hybrid Runbook Worker não pôde gerar um certificado autoassinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Cenário: falha na atualização agendada com um erro MaintenanceWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção padrão para atualizações é de 120 minutos. Você pode aumentar a janela de manutenção para um máximo de 6 horas ou 360 minutos.

### <a name="resolution"></a>Resolução

Edite todas as implantações de atualização agendadas com falha e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, consulte [instalar atualizações](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Cenário: a máquina é mostrada como "não avaliada" e mostra uma exceção HRESULT

### <a name="issue"></a>Problema

* Você tem computadores que mostram como `Not assessed` sob **conformidade**e você vê uma mensagem de exceção abaixo deles.
* Você verá um código de erro HRESULT no Portal.

### <a name="cause"></a>Causa

O agente de atualização (Windows Update agente no Windows; o Gerenciador de pacotes para uma distribuição do Linux) não está configurado corretamente. Gerenciamento de Atualizações se baseia no agente de atualização da máquina para fornecer as atualizações necessárias, o status do patch e os resultados dos patches implantados. Sem essas informações, Gerenciamento de Atualizações não pode relatar corretamente os patches necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente executar atualizações localmente no computador. Se essa operação falhar, isso normalmente significa que há um erro de configuração do agente de atualização.

Esse problema é frequentemente causado por problemas de firewall e configuração de rede. Use as verificações a seguir para corrigir o problema.

* Para o Linux, verifique a documentação apropriada para ter certeza de que você pode acessar o ponto de extremidade de rede do seu repositório de pacotes.

* Para o Windows, verifique a configuração do agente, conforme listado em [atualizações não está baixando do ponto de extremidade da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se os computadores estiverem configurados para Windows Update, verifique se você pode acessar os pontos de extremidade descritos em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se os computadores estiverem configurados para o Windows Server Update Services (WSUS), verifique se você pode acessar o servidor do WSUS configurado pela [chave do registro WUServer](/windows/deployment/update/waas-wu-settings).

Se você vir um HRESULT, clique duas vezes na exceção exibida em vermelho para ver a mensagem de exceção inteira. Examine a tabela a seguir para obter possíveis soluções ou ações recomendadas.

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquise o código de erro relevante na [lista de códigos de erro do Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Isso indica problemas de conectividade de rede. Verifique se o computador tem conectividade de rede para Gerenciamento de Atualizações. Consulte a seção [planejamento de rede](../automation-update-management.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou o sistema estava sendo desligado.|
|`0x8024002E`| Windows Update serviço está desabilitado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, verifique se os valores do registro `WUServer` para `WUStatusServer` e na `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` chave do registro especificam o servidor WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em conversar com um servidor WSUS configurado. Verifique as configurações do WSUS e verifique se o serviço pode ser acessado do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verifique se o serviço de Windows Update (wuauserv) está em execução e não está desabilitado.        |
|`0x80070005`| Um erro de acesso negado pode ser causado por qualquer um dos seguintes:<br> Computador infectado<br> Windows Update configurações não configuradas corretamente<br> Erro de permissão de arquivo com a pasta%WinDir%\SoftwareDistribution<br> Espaço em disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Execute uma pesquisa na Internet para obter soluções possíveis e trabalhe com o suporte de ti local.         |

A revisão do arquivo **%windir%\Windowsupdate.log** também pode ajudá-lo a determinar possíveis causas. Para obter mais informações sobre como ler o log, consulte [como ler o arquivo windowsupdate. log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Você também pode baixar e executar a [solução de problemas Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se há problemas com Windows Update no computador.

> [!NOTE]
> A documentação da [solução de problemas Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que ele é para uso em clientes Windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: a execução de atualização retorna o status de falha (Linux)

### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

### <a name="cause"></a>Causa

Possíveis causas:

* O Gerenciador de pacotes não está íntegro.
* O agente de atualização (WUA para Windows, Gerenciador de pacotes específico do distribuição para Linux) está configurado incorretamente.
* Os pacotes específicos estão interferindo com a aplicação de patch baseada em nuvem.
* O computador está inacessível.
* As atualizações tinham dependências que não foram resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma execução de atualização depois que ela for iniciada com êxito, [Verifique a saída do trabalho](../manage-update-multi.md#view-results-of-an-update-deployment) do computador afetado na execução. Você pode encontrar mensagens de erro específicas de seus computadores nos quais você pode pesquisar e tomar medidas. O Gerenciamento de Atualizações requer que o gerenciador de pacotes seja saudável para implantações de atualização bem-sucedidas.

Se patches, pacotes ou atualizações específicos forem vistos imediatamente antes da falha do trabalho, você poderá tentar [excluir](../automation-tutorial-update-management.md#schedule-an-update-deployment) esses itens da próxima implantação de atualização. Para coletar informações de log de Windows Update, consulte [Windows Update arquivos de log](/windows/deployment/update/windows-update-logs).

Se você não puder resolver um problema de aplicação de patch, faça uma cópia do arquivo **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** e preserve-o para fins de solução de problemas antes de iniciar a próxima implantação de atualização.

## <a name="patches-arent-installed"></a>Os patches não estão instalados

### <a name="machines-dont-install-updates"></a>Os computadores não estão instalando atualizações

Tente executar as atualizações diretamente no computador. Se o computador não puder aplicar as atualizações, consulte a [lista de possíveis erros no guia de solução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se as atualizações forem executadas localmente, tente remover e reinstalar o agente no computador seguindo as orientações em [remover uma VM do gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que as atualizações estão disponíveis, mas elas não são mostradas como disponíveis em meus computadores

Isso geralmente acontece se as máquinas estiverem configuradas para obter atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e Configuration Manager não tiverem aprovado as atualizações.

Você pode verificar se os computadores estão configurados para o WSUS e o SCCM por meio da `UseWUServer` referência cruzada da chave do registro para as chaves do registro na [seção configurar atualizações automáticas editando o registro deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se as atualizações não forem aprovadas no WSUS, elas não serão instaladas. Você pode verificar se há atualizações não aprovadas em Log Analytics executando a consulta a seguir.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações aparecem como instaladas, mas não as encontro em meu computador

Normalmente, as atualizações são substituídas por outras atualizações. Para obter mais informações, consulte a [atualização é substituída](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) no guia de solução de problemas de Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalando atualizações por classificação no Linux

Implantar atualizações no Linux por classificação (“Atualizações críticas e de segurança”) tem limitações importantes, especialmente no CentOS. Essas limitações estão documentadas na [página Visão geral do gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está consistentemente ausente

KB2267602 é a [atualização de definição do Windows Defender](https://www.microsoft.com/wdsi/definitions). Ele é atualizado diariamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar o problema ou não conseguir resolver o problema, tente um dos canais a seguir para obter suporte adicional.

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
