---
title: Solução de problemas Gerenciamento de atualização de automação do Azure
description: Saiba como solucionar problemas e resolver problemas com a solução de gerenciamento de atualizações no Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/17/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 91ecff311b8820d3b97e1de0e4b4e87c150e749b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678933"
---
# <a name="troubleshoot-issues-with-the-update-management-solution"></a>Solucionando problemas com a solução De gerenciamento de atualizações

Este artigo discute problemas que você pode encontrar ao usar a solução Update Management. Há um agente solucionador de problemas para o agente híbrido do Trabalhador do Runbook para determinar o problema subjacente. Para saber mais sobre o solução de [problemas, consulte Problemas de solução de problemas do agente de atualização do Windows](update-agent-issues.md) e [problemas do agente de atualização do Linux](update-agent-issues-linux.md). Para outros problemas de onboarding, consulte [Solução de solução de solução de solução de solução de sope](onboarding.md).

>[!NOTE]
>Se você encontrar problemas ao embarcar na solução em uma máquina virtual (VM), verifique o registro do **Gerente de Operações** em **Registros de Aplicativos e Serviços** na máquina local. Procure eventos com id de evento 4502 e detalhes do evento que contenham `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a>Cenário: Você recebe o erro "Falha ao ativar a solução Atualizar"

### <a name="issue"></a>Problema

Quando você tenta ativar a solução Update Management em sua conta de Automação, você recebe o seguinte erro:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* Os requisitos de firewall de rede para o agente Log Analytics podem não estar configurados corretamente. Essa situação pode fazer com que o agente falhe ao resolver as URLs DNS.

* A segmentação da solução está mal configurada e a máquina não está recebendo atualizações como esperado.

* Você também pode notar que a `Non-compliant` máquina mostra um status de em **Conformidade**. Ao mesmo tempo, o Agent Desktop `Disconnected` **Analytics** informa o agente como .

### <a name="resolution"></a>Resolução

* Execute o solucionador de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux,](update-agent-issues-linux.md#troubleshoot-offline)dependendo do sistema operacional.

* Vá para [o planejamento da rede](../automation-hybrid-runbook-worker.md#network-planning) para saber quais endereços e portas devem ser autorizados para que o Gerenciamento de Atualizações funcione.  

* Vá para [o planejamento de rede](../../azure-monitor/platform/log-analytics-agent.md#network-requirements) para saber quais endereços e portas devem ser permitidos para que o agente do Log Analytics funcione.

* Verifique se há problemas de configuração de escopo. [A configuração do escopo](../automation-onboard-solutions-from-automation-account.md#scope-configuration) determina quais máquinas são configuradas para a solução. Se sua máquina estiver aparecendo no seu espaço de trabalho, mas não no Portal de Gerenciamento de Atualizações **, você precisará definir a configuração do escopo para atingir as máquinas. Para saber mais sobre a configuração do escopo, consulte [máquinas onboard no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Remova a configuração do trabalhador seguindo as etapas de [exclusão do executo híbrido](../automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker). 

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: Atualização superseded indicada como ausente no Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Atualizações antigas estão aparecendo para uma conta de Automação como desaparecidas, mesmo que tenham sido substituídas. Uma atualização substituída é aquela que você não precisa instalar porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. O Update Management ignora a atualização substituída e torna-a não aplicável em favor da atualização de suinagem. Para obter informações sobre um problema relacionado, consulte [Atualização é substituída](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

As atualizações substituídas não são corretamente indicadas como recusadas para que possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização substituída se torna 100% não aplicável, você deve `Declined`alterar o estado de aprovação dessa atualização para . Para alterar o estado de aprovação de todas as suas atualizações:

1. Na conta Automação, selecione **Atualizar gerenciamento** para visualizar o status da máquina. Consulte [Ver Exibir avaliações de atualização](../manage-update-multi.md#view-an-update-assessment).

2. Verifique a atualização substituída para ter certeza de que não é 100% aplicável. 

3. Marque a atualização como recusada a menos que você tenha uma pergunta sobre a atualização. 

4. Selecione **Computadores** e, na coluna **Conformidade,** force uma nova verificação para conformidade. Consulte [Gerenciar atualizações para várias máquinas](../manage-update-multi.md).

5. Repita os passos acima para outras atualizações substituídas.

6. Execute o assistente de limpeza para excluir arquivos das atualizações recusadas. 

7. Para o Windows Server Update Services (WSUS), limpe manualmente todas as atualizações substituídas para atualizar a infra-estrutura.

8. Repita este procedimento regularmente para corrigir o problema do display e minimizar a quantidade de espaço em disco usado para o gerenciamento de atualizações.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Cenário: Máquinas não aparecem no portal em Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Suas máquinas têm os seguintes sintomas:

* Sua máquina `Not configured` mostra a partir da visão de gerenciamento de atualizações de uma VM.

* Suas máquinas estão faltando na exibição de gerenciamento de atualizações da sua conta azure Automação.

* Você tem máquinas `Not assessed` que mostram como em **Conformidade**. No entanto, você vê dados de batimentos cardíacos nos registros do Monitor Do Azure para o Hybrid Runbook Worker, mas não para o Gerenciamento de Atualizações.

### <a name="cause"></a>Causa

Esse problema pode ser causado por problemas de configuração local ou por configuração de escopo configurada incorretamente. Possíveis causas específicas são:

* Você pode ter que recadastrar e reinstalar o Hybrid Runbook Worker.

* Você pode ter definido uma cota em seu espaço de trabalho que foi alcançada e isso está impedindo mais armazenamento de dados.

### <a name="resolution"></a>Resolução

1. Execute o solucionador de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux,](update-agent-issues-linux.md#troubleshoot-offline)dependendo do sistema operacional.

2. Certifique-se de que sua máquina está reportando ao espaço de trabalho correto. Para obter orientações sobre como verificar esse aspecto, consulte [Verificar a conectividade do agente ao Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Certifique-se também de que este espaço de trabalho esteja vinculado à sua conta do Azure Automation. Para confirmar, vá para sua conta de Automação e selecione **o espaço de trabalho Vinculado** em Recursos **Relacionados**.

3. Certifique-se de que as máquinas apareçam no espaço de trabalho do Log Analytics vinculado à sua conta de Automação. Execute a seguinte consulta no espaço de trabalho do Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Se você não ver sua máquina nos resultados da consulta, ela não fez check-in recentemente. Provavelmente há um problema de configuração local e você deve [reinstalar o agente.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows) 

5. Se a máquina aparecer nos resultados da consulta, verifique se há problemas de configuração de escopo. [A configuração do](../automation-onboard-solutions-from-automation-account.md#scope-configuration) escopo determina quais máquinas estão configuradas para a solução. 

6. Se sua máquina estiver aparecendo no seu espaço de trabalho, mas não no Gerenciamento de Atualizações, você deve configurar a configuração de escopo para atingir a máquina. Para aprender como fazer isso, consulte [máquinas a bordo no espaço de trabalho](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

7. Em seu espaço de trabalho, execute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Se você `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` conseguir um resultado, a cota definida em seu espaço de trabalho foi atingida, o que impediu que os dados fossem salvos. Em seu espaço de trabalho, vá para o **gerenciamento de volume de dados** sob uso e custos **estimados,** e altere ou remova a cota.

9. Se o seu problema ainda não estiver resolvido, siga as etapas em [Implantar um Trabalhador de Runbook Híbrido do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para Windows. Para Linux, siga os passos em [Implantar um Linux Hybrid Runbook Worker](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Não é possível registrar provedor de recursos de automação para assinaturas

### <a name="issue"></a>Problema

Quando você trabalha com soluções em sua conta de Automação, ocorre o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

O provedor de recursos de Automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para cadastrar o provedor de recursos de Automação, siga estas etapas no portal Azure.

1. Na lista de serviços do Azure na parte inferior do portal, selecione **Todos os serviços**e selecione Assinaturas no grupo de **serviços** Gerais.

2. Selecione sua assinatura.

3. Em **Configurações,** selecione **Provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos Microsoft.Automation está registrado.

5. Se não estiver listado, registre o provedor Microsoft.Automation seguindo as etapas em [Resolver erros para registro de provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Cenário: Atualização programada com um cronograma dinâmico perdeu algumas máquinas

### <a name="issue"></a>Problema

As máquinas incluídas em uma pré-visualização de atualização não aparecem todas na lista de máquinas corrigidas durante uma execução programada.

### <a name="cause"></a>Causa

Este problema pode ter uma das seguintes causas:

* As assinaturas definidas no escopo em uma consulta dinâmica não estão configuradas para o provedor de recursos de Automação registrado.

* As máquinas não estavam disponíveis ou não tinham etiquetas apropriadas quando o cronograma foi executado.

### <a name="resolution"></a>Resolução

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Assinaturas não configuradas para provedor de recursos de automação registrado

Se sua assinatura não estiver configurada para o provedor de recursos de Automação, você não poderá consultar ou buscar informações sobre máquinas nessa assinatura. Use as seguintes etapas para verificar o registro da assinatura.

1. No [portal Azure,](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)acesse a lista de serviços do Azure.

2. Selecione **Todos os serviços**e selecione Assinaturas no grupo de **serviços** gerais. 

3. Encontre a assinatura definida no escopo de sua implantação.

4. Em **Configurações,** escolha **Provedores de recursos**.

5. Verifique se o provedor de recursos Microsoft.Automation está registrado.

6. Se não estiver listado, registre o provedor Microsoft.Automation seguindo as etapas em [Resolver erros para registro de provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Máquinas não disponíveis ou não marcadas corretamente quando o cronograma executado

Use o procedimento a seguir se sua assinatura estiver configurada para o provedor de recursos de Automação, mas executando o cronograma de atualização com os [grupos dinâmicos](../automation-update-management-groups.md) especificados perdeu algumas máquinas.

1. No portal Azure, abra a conta automação e selecione **Gerenciamento de atualizações**.

2. Verifique [o histórico do gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/manage-update-multi#view-results-of-an-update-deployment) para determinar a hora exata em que a implantação da atualização foi executada. 

3. Para máquinas que você suspeita ter sido perdidas pelo Update Management, use o Azure Resource Graph (ARG) para [localizar alterações na máquina](https://docs.microsoft.com/azure/governance/resource-graph/how-to/get-resource-changes#find-detected-change-events-and-view-change-details). 

4. Procure mudanças durante um período considerável, como um dia, antes da implantação da atualização ser executada.

5. Verifique os resultados da pesquisa para obter quaisquer alterações sistêmicas, como excluir ou atualizar alterações, para as máquinas neste período. Essas alterações podem alterar o status da máquina ou tags para que as máquinas não sejam selecionadas na lista da máquina quando as atualizações forem implantadas.

6. Ajuste as máquinas e as configurações de recursos conforme necessário para corrigir os problemas de status da máquina ou tag.

7. Reexecute o cronograma de atualização para garantir que a implantação com os grupos dinâmicos especificados inclua todas as máquinas.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Cenário: Máquinas esperadas não aparecem na pré-visualização para grupo dinâmico

### <a name="issue"></a>Problema

As máquinas virtuais para escopos selecionados de um grupo dinâmico não estão aparecendo na lista de visualização do portal Azure. Esta lista consiste em todas as máquinas recuperadas por uma consulta ARG para os escopos selecionados. Os escopos são filtrados para máquinas que possuem Trabalhadores híbridos de runbook instalados e para os quais você tem permissões de acesso. 

### <a name="cause"></a>Causa
 
Aqui estão as possíveis causas para este problema:

* Você não tem o acesso correto nos escopos selecionados.
* A consulta ARG não recupera as máquinas esperadas.
* O Híbrido Runbook Worker não está instalado nas máquinas.

### <a name="resolution"></a>Resolução 

#### <a name="incorrect-access-on-selected-scopes"></a>Acesso incorreto em escopos selecionados

O portal Azure exibe apenas máquinas para as quais você tem acesso à gravação em um determinado escopo. Se você não tiver o acesso correto para um escopo, consulte [Tutorial: Conceda a um usuário acesso aos recursos do Azure usando o RBAC e o portal Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

#### <a name="arg-query-doesnt-return-expected-machines"></a>Consulta ARG não devolve máquinas esperadas

Siga as etapas abaixo para saber se suas consultas estão funcionando corretamente.

1. Execute uma consulta ARG formatada conforme mostrado abaixo na lâmina exploradora resource graph no portal Azure. Esta consulta imita os filtros selecionados quando criou o grupo dinâmico no Gerenciamento de Atualizações. Veja [Usar grupos dinâmicos com gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management-groups). 

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
 
2. Verifique se as máquinas que você está procurando estão listadas nos resultados da consulta. 

3. Se as máquinas não estão listadas, provavelmente há um problema com o filtro selecionado no grupo dinâmico. Ajuste a configuração do grupo conforme necessário.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Trabalhador de runbook híbrido não instalado em máquinas

As máquinas aparecem nos resultados da consulta ARG, mas ainda não aparecem na pré-visualização dinâmica do grupo. Neste caso, as máquinas podem não ser designadas como trabalhadores híbridos e, portanto, não podem executar trabalhos de Automação e Gerenciamento de Atualizações do Azure. Para garantir que as máquinas que você espera ver sejam configuradas como Trabalhadores híbridos do runbook:

1. No portal Azure, acesse a conta de Automação para uma máquina que não está aparecendo corretamente.

2. Selecione **grupos de trabalhadores híbridos** em **Automação de Processos**.

3. Selecione a guia **Grupos de trabalhadores híbridos** do sistema.

4. Valide que o trabalhador híbrido está presente para essa máquina.

5. Se a máquina não estiver configurada como um trabalhador híbrido, faça ajustes usando instruções em [Automatizar recursos em seu data center ou nuvem usando o Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker).

6. Junte-se à máquina ao grupo Hybrid Runbook Worker.

7. Repita os passos acima para todas as máquinas que não foram exibidas na pré-visualização.

## <a name="scenario-components-for-update-management-solution-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Cenário: Componentes para solução de gerenciamento de atualização ativados, enquanto a VM continua a mostrar como sendo configurada

### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a integração:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* A comunicação com a conta de Automação está sendo bloqueada.

* Há um nome de computador duplicado com diferentes IDs de computador de origem. Esse cenário ocorre quando uma VM com um nome de computador específico é criada em diferentes grupos de recursos e está reportando ao mesmo espaço de trabalho do Agente Logístico na assinatura.

* A imagem vm que está sendo a bordo pode vir de uma máquina clonada que não foi preparada com preparação do sistema (sysprep) com o agente Log Analytics para Windows instalado.

### <a name="resolution"></a>Resolução

Para ajudar a determinar o problema exato com a VM, execute a seguinte consulta no espaço de trabalho do Log Analytics que está vinculado à sua conta de Automação.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Comunicação com conta de Automação bloqueada

Vá para [o planejamento da rede](../automation-update-management.md#ports) para saber quais endereços e portas devem ser autorizados para que o Gerenciamento de Atualizações funcione.

#### <a name="duplicate-computer-name"></a>Nome do computador duplicado

Renomeie suas VMs para garantir nomes únicos em seu ambiente.

#### <a name="onboarded-image-from-cloned-machine"></a>Imagem a bordo de máquina clonada

Se você estiver usando uma imagem clonada, diferentes nomes de computador têm a mesma ID de computador de origem. Nesse caso:

1. No espaço de trabalho do Log Analytics, remova `MicrosoftDefaultScopeConfig-Updates` a VM da pesquisa salva para a configuração do escopo, se ela for mostrada. As pesquisas salvas podem ser encontradas em **Geral** no workspace.

2. Execute o cmdlet a seguir.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Corra `Restart-Service HealthService` para reiniciar o serviço de saúde. Esta operação recria a chave e gera um novo UUID.

4. Se essa abordagem não funcionar, execute o Sysprep na imagem primeiro e, em seguida, instale o MMA.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Cenário: Você recebe um erro de assinatura vinculado ao criar uma implantação de atualização para máquinas em outro inquilino do Azure

### <a name="issue"></a>Problema

Você encontra o seguinte erro quando tenta criar uma implantação de atualização para máquinas em outro inquilino do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem VMs do Azure em outro inquilino que está incluído em uma implantação de atualização.

### <a name="resolution"></a>Resolução

Use a solução a seguir para agendar esses itens. Você pode usar o [cmdlet New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) com o `ForUpdateConfiguration` parâmetro para criar um cronograma. Em seguida, use o [cmdlet New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passe `NonAzureComputer` as máquinas no outro inquilino para o parâmetro. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Cenário: Reinicializações inexplicáveis

### <a name="issue"></a>Problema

Mesmo que você tenha definido a opção De controle de **reinicialização** para **Never Reboot,** as máquinas ainda estão reiniciando depois que as atualizações são instaladas.

### <a name="cause"></a>Causa

O Windows Update pode ser modificado por várias chaves de registro, qualquer uma das quais pode modificar o comportamento de reinicialização.

### <a name="resolution"></a>Resolução

Revise as chaves de registro listadas em [Configuração de Atualizações Automáticas editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) chaves de registro e registro usadas para gerenciar a [reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) para garantir que suas máquinas estejam configuradas corretamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Cenário: Máquina mostra "Falha ao iniciar" em uma implantação de atualização

### <a name="issue"></a>Problema

Uma máquina `Failed to start` mostra um status. Ao visualizar os detalhes específicos da máquina, verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Esse problema pode ocorrer por um dos seguintes motivos:

* A máquina não existe mais.
* A máquina está desligada e inalcançável.
* A máquina tem um problema de conectividade de rede e, portanto, o trabalhador híbrido na máquina é inalcançável.
* Houve uma atualização no MMA que mudou o ID do computador de origem.
* Sua corrida de atualização foi estrangulada se você atingir o limite de 2000 empregos simultâneos em uma conta de Automação. Cada implantação é considerada um trabalho, e cada máquina em uma implantação de atualização conta como um trabalho. Qualquer outro trabalho de automação ou implantação de atualização atualmente em execução em sua conta de automação conta para o limite de trabalho simultâneo.

### <a name="resolution"></a>Resolução

Quando aplicável, use [grupos dinâmicos](../automation-update-management-groups.md) para suas implantações de atualização. Além disso, você pode dar as seguintes etapas.

1. Verifique se a máquina ainda existe e é acessível. 
2. Se a máquina não existir, edite sua implantação e remova a máquina.
3. Consulte a seção [de planejamento](../automation-update-management.md#ports) de rede para obter uma lista de portas e endereços necessários para o Gerenciamento de Atualizações e, em seguida, verifique se sua máquina atende a esses requisitos.
4. Verifique a conectividade com o Híbrido Runbook Worker usando o solucionador de problemas do agente Hybrid Runbook Worker. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md).
5. Execute a seguinte consulta no Log Analytics para encontrar máquinas em seu ambiente para as quais o ID do computador de origem foi alterado. Procure computadores que `Computer` tenham o `SourceComputerId` mesmo valor, mas um valor diferente.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. Depois de encontrar as máquinas afetadas, edite as implantações de atualização `SourceComputerId` que visam essas máquinas e, em seguida, remova-as e leia-as de modo que reflete o valor correto.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Cenário: Atualizações são instaladas sem uma implantação

### <a name="issue"></a>Problema

Quando você inscreve uma máquina do Windows no Gerenciamento de atualizações, você vê atualizações instaladas sem uma implantação.

### <a name="cause"></a>Causa

No Windows, as atualizações são instaladas automaticamente assim que estiverem disponíveis. Esse comportamento pode causar confusão se você não agendar uma atualização para ser implantada na máquina.

### <a name="resolution"></a>Resolução

A `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` tecla de registro é `auto download and install`padrão para uma configuração de 4: .

Para clientes de Gerenciamento de Atualizações, recomendamos definir esta chave para 3: `auto download but do not auto install`.

Para obter mais informações, consulte [Configurando atualizações automáticas](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: a máquina já está registrada em uma conta diferente

### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

A máquina já foi embarcada para outro espaço de trabalho para gerenciamento de atualizações.

### <a name="resolution"></a>Resolução

1. Siga as etapas em [Máquinas não apareçam no portal em Gerenciamento de Atualizações](#nologs) para ter certeza de que a máquina está reportando ao espaço de trabalho correto.
2. Limpe artefatos na máquina [excluindo o grupo híbrido de runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)e tente novamente.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Cenário: Máquina não pode se comunicar com o serviço

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

Um proxy, gateway ou firewall pode estar bloqueando a comunicação da rede. 

### <a name="resolution"></a>Resolução

Revise sua rede e certifique-se de que portas e endereços apropriados sejam permitidos. Consulte [os requisitos](../automation-hybrid-runbook-worker.md#network-planning) de rede para uma lista de portas e endereços que são exigidos pelo Update Management e hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Cenário: não é possível criar certificado autoassinado

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Hybrid Runbook Worker não poderia gerar um certificado auto-assinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema leu acesso à pasta **C:\ProgramData\Microsoft\RSA** e tente novamente.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Cenário: A atualização programada falhou com um erro de MaintenanceWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção padrão para atualizações é de 120 minutos. Você pode aumentar a janela de manutenção para um máximo de 6 horas, ou 360 minutos.

### <a name="resolution"></a>Resolução

Edite quaisquer implantações de atualização programadas com falha e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, consulte [Instalar atualizações](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Cenário: Máquina mostra como "Não avaliado" e mostra uma exceção HRESULT

### <a name="issue"></a>Problema

* Você tem máquinas `Not assessed` que mostram como em **Conformidade,** e você vê uma mensagem de exceção abaixo delas.
* Você vê um código de erro HRESULT no portal.

### <a name="cause"></a>Causa

O Update Agent (Windows Update Agent on Windows; o gerenciador de pacotes para uma distribuição Linux) não está configurado corretamente. O Update Management conta com o Agente de Atualização da máquina para fornecer as atualizações necessárias, o status do patch e os resultados dos patches implantados. Sem essas informações, o Update Management não pode informar corretamente os patches necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente realizar atualizações localmente na máquina. Se esta operação falhar, normalmente significa que há um erro de configuração do agente de atualização.

Esse problema é frequentemente causado por problemas de configuração de rede e firewall. Use as seguintes verificações para corrigir o problema.

* Para linux, verifique a documentação apropriada para ter certeza de que você pode chegar ao ponto final da rede do seu repositório de pacotes.

* Para o Windows, verifique a configuração do seu agente conforme listado em [Atualizações não estão baixando do ponto final da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se as máquinas estiverem configuradas para o Windows Update, certifique-se de que você pode alcançar os pontos finais descritos em [Problemas relacionados ao HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se as máquinas estiverem configuradas para o Windows Server Update Services (WSUS), certifique-se de que você pode alcançar o servidor WSUS configurado pela [chave de registro WUServer](/windows/deployment/update/waas-wu-settings).

Se você vir um HRESULTADO, clique duas vezes na exceção exibida em vermelho para ver toda a mensagem de exceção. Revise a tabela a seguir para obter soluções potenciais ou ações recomendadas.

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquise o código de erro relevante na lista de [códigode erro de atualização](https://support.microsoft.com/help/938205/windows-update-error-code-list) do Windows para encontrar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Isso indica problemas de conectividade de rede. Certifique-se de que sua máquina tenha conectividade de rede com o Gerenciamento de Atualizações. Consulte a seção [de planejamento](../automation-update-management.md#ports) de rede para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou sistema estava sendo desligado.|
|`0x8024002E`| O serviço Windows Update está desativado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, `WUServer` certifique-se de que os valores de registro para e `WUStatusServer` sob a `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` chave de registro especifiquem o servidor WSUS correto.        |
|`0x80072EE2`|Há um problema de conectividade de rede ou um problema em falar com um servidor WSUS configurado. Verifique as configurações do WSUS e certifique-se de que o serviço está acessível a partir do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Certifique-se de que o serviço Windows Update (wuauserv) está em execução e não está desativado.        |
|`0x80070005`| Um erro de acesso negado pode ser causado por qualquer um dos seguintes:<br> Computador infectado<br> Configurações do Windows Update não configuradas corretamente<br> Erro de permissão de arquivo com %WinDir%\Pasta de distribuição de software<br> Espaço em disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Faça uma pesquisa na internet em busca de possíveis soluções e trabalhe com seu suporte de TI local.         |

A revisão do **arquivo %Windir%\Windowsupdate.log** também pode ajudá-lo a determinar possíveis causas. Para obter mais informações sobre como ler o registro, consulte [Como ler o arquivo Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Você também pode baixar e executar o [solucionador de problemas](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) do Windows Update para verificar se há problemas com o Windows Update na máquina.

> [!NOTE]
> A documentação [do solucionador de problemas](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) do Windows Update indica que ele é para uso em clientes Windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: Atualização de execução retorna Status falhado (Linux)

### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

### <a name="cause"></a>Causa

Possíveis causas:

* O gerente do pacote não é saudável.
* O Update Agent (WUA para Windows, gerenciador de pacotes específico de distro para Linux) está mal configurado.
* Pacotes específicos estão interferindo com patches baseados em nuvem.
* A máquina é inalcançável.
* As atualizações tinham dependências que não foram resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma execução de atualização após o início do [trabalho, verifique a saída](../manage-update-multi.md#view-results-of-an-update-deployment) de trabalho da máquina afetada na execução. Você pode encontrar mensagens de erro específicas de suas máquinas que você pode pesquisar e tomar medidas. O Gerenciamento de Atualizações requer que o gerenciador de pacotes seja saudável para implantações de atualização bem-sucedidas.

Se patches, pacotes ou atualizações específicas forem vistos imediatamente antes do trabalho falhar, você pode tentar [excluir](../automation-tutorial-update-management.md#schedule-an-update-deployment) esses itens da próxima implantação de atualização. Para coletar informações de log do Windows Update, consulte [arquivos de log do Windows Update](/windows/deployment/update/windows-update-logs).

Se você não puder resolver um problema de correção, faça uma cópia do **arquivo /var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** e preserve-o para fins de solução de problemas antes que a próxima implantação de atualização seja iniciada.

## <a name="patches-arent-installed"></a>Os patches não são instalados

### <a name="machines-dont-install-updates"></a>Os computadores não estão instalando atualizações

Tente executar as atualizações diretamente no computador. Se a máquina não puder aplicar as atualizações, consulte a [lista de possíveis erros no guia de solução de problemas](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).

Se as atualizações forem executadas localmente, tente remover e reinstalar o agente na máquina seguindo a orientação em [Remover uma VM do Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Eu sei que as atualizações estão disponíveis, mas elas não aparecem como disponíveis nas minhas máquinas

Isso geralmente acontece se as máquinas estiverem configuradas para receber atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e o Gerenciador de Configuração não aprovaram as atualizações.

Você pode verificar se as máquinas estão configuradas para WSUS e `UseWUServer` SCCM cruzando a chave de registro para as chaves de registro na [Configuração de Atualizações Automáticas editando a seção Registro deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se as atualizações não forem aprovadas no WSUS, elas não estarão instaladas. Você pode verificar se há atualizações não aprovadas no Log Analytics executando a seguinte consulta.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações aparecem como instaladas, mas não as encontro em meu computador

Normalmente, as atualizações são substituídas por outras atualizações. Para obter mais informações, consulte Atualização é substituído no guia de solução de [problemas](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) do Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalando atualizações por classificação no Linux

Implantar atualizações no Linux por classificação (“Atualizações críticas e de segurança”) tem limitações importantes, especialmente no CentOS. Essas limitações são documentadas na [página visão geral do Gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está constantemente faltando

KB2267602 é a [atualização de definição do Windows Defender](https://www.microsoft.com/wdsi/definitions). É atualizado diariamente.

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional.

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
