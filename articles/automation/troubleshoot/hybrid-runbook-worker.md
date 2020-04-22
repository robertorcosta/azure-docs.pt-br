---
title: Solução de problemas Trabalhadores híbridos de runbook de automação do Azure
description: Este artigo fornece informações para solução de problemas Azure Automation Hybrid Runbook Workers.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679329"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de trabalhadores de runbooks híbridos

Este artigo fornece informações sobre como solucionar problemas com os trabalhadores de runbook híbridos.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="general"></a>Geral

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, recebe trabalhos de runbook e status de relatórios. Para o Windows, este agente é o agente log analytics para Windows. Para Linux, é o agente log analytics para Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Cenário: a execução do Runbook falha

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe o seguinte erro.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Seu manual é suspenso logo após tentar executar três vezes. Existem condições que podem interromper a conclusão do manual. A mensagem de erro relacionada pode não incluir nenhuma informação adicional.

#### <a name="cause"></a>Causa

Estas são as possíveis causas:

* Os runbooks não podem autenticar com recursos locais.
* O Hybrid Worker está usando um proxy ou firewall.
* O computador configurado para executar o Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída a ***.azure-automation.net** na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes que o trabalhador esteja configurado para hospedar esse recurso. Os runbooks e o processo de fundo que eles usam podem fazer com que o sistema seja usado em excesso e cause atrasos ou intervalos de tempo.

Confirme se o computador para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore o uso de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Qualquer pressão de memória ou CPU pode indicar a necessidade de atualizar recursos. Você também pode selecionar um recurso de computação diferente que suporte os requisitos mínimos e a escala quando as demandas de carga de trabalho indicam que um aumento é necessário.

Verifique o registro de eventos **Microsoft-SMA** para obter um evento correspondente com a descrição `Win32 Process Exited with code [4294967295]`. A causa deste erro é que você não configurou a autenticação em seus runbooks ou especificou as credenciais Run As para o grupo Hybrid Runbook Worker. Revise as permissões do runbook em [Executar runbooks em um Trabalhador de Runbook Híbrido](../automation-hrw-run-runbooks.md) para confirmar que você configurou corretamente a autenticação para seus runbooks.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Cenário: Evento 15011 no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

O Hybrid Runbook Worker recebe o evento 15011, indicando que um resultado de consulta não é válido. O erro a seguir aparece quando o trabalhador tenta abrir uma conexão com o [servidor SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

O Hybrid Runbook Worker não foi configurado corretamente para a solução de implantação automatizada. Esta solução contém uma peça que conecta a VM ao espaço de trabalho log analytics. O script PowerShell procura o espaço de trabalho na assinatura com o nome fornecido. Neste caso, o espaço de trabalho do Log Analytics está em uma assinatura diferente. O script não consegue encontrar o espaço de trabalho e tenta criar um, mas o nome já está tomado. Assim, a implantação falha.

#### <a name="resolution"></a>Resolução

Você tem duas opções para resolver este problema:

* Modifique o script PowerShell para procurar o espaço de trabalho do Log Analytics em outra assinatura. Esta é uma boa solução se você planeja implantar muitas máquinas Hybrid Runbook Worker no futuro.

* Configure manualmente a máquina do trabalhador para ser executada em uma caixa de areia do Orquestrador. Em seguida, execute um runbook criado na conta Azure Automation no trabalhador para testar a funcionalidade.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Cenário: As VMs do Windows Azure caíram automaticamente do grupo de trabalhadores híbridos

#### <a name="issue"></a>Problema

Você não pode ver o Hybrid Runbook Worker ou as VMs quando a máquina do trabalhador está desligada há muito tempo.

#### <a name="cause"></a>Causa

A máquina Hybrid Runbook Worker não rastrea a Azure Automation há mais de 30 dias. Como resultado, a Automação eliminou o grupo Hybrid Runbook Worker ou o grupo Trabalhador do Sistema. 

#### <a name="resolution"></a>Resolução

Inicie a máquina do trabalhador e, em seguida, registre-a novamente na Azure Automation. Consulte as instruções para instalar o ambiente do runbook e conectar-se à Automação Azure no [Implantar um Trabalhador de Runbook Híbrido do Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Cenário: Nenhum certificado foi encontrado na loja de certificados no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Um runbook em execução em um Hybrid Runbook Worker falha com a seguinte mensagem de erro.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta usar uma [conta Run As](../manage-runas-account.md) em um runbook que é executado em um Trabalhador de Runbook Híbrido onde o certificado de conta Run As não está presente. Os trabalhadores híbridos do runbook não têm o ativo de certificado localmente por padrão. A conta Run As exige que esse ativo opere corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Hybrid Runbook Worker for uma VM Do Azure, você poderá usar [identidades gerenciadas para recursos do Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Esse cenário simplifica a autenticação, permitindo que você se autentique nos recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Run As. Quando o Hybrid Runbook Worker é uma máquina no local, você precisa instalar o certificado de conta Run As na máquina. Para saber como instalar o certificado, consulte as etapas para executar o runbook PowerShell **Export-RunAsCertificateToHybridWorker** em [execução de runbooks em um Trabalhador de runbook híbrido](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Cenário: Erro 403 durante o registro do Hybrid Runbook Worker

#### <a name="issue"></a>Problema

A fase inicial de registro do trabalhador falha e você recebe o seguinte erro (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Estas são as possíveis causas:

* Há uma id de espaço de trabalho digitada mal digitada ou chave de espaço de trabalho (principal) nas configurações do agente. 

* O Hybrid Runbook Worker não pode baixar a configuração, causando um erro de vinculação da conta. Quando o Azure habilita soluções, ele suporta apenas determinadas regiões para vincular um espaço de trabalho do Log Analytics e uma conta de Automação. Também é possível que uma data e/ou hora incorretas seja definida no computador. Se o tempo for de +/-15 minutos a partir do momento atual, o onboarding falhará.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-idkey"></a>ID/chave de espaço de trabalho mal digitada
Para verificar se o ID de espaço de trabalho do agente ou a chave do espaço de trabalho foi digitado errado, consulte [Adicionar ou remover um espaço de trabalho – agente do Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) para o agente windows ou Adicionar ou remover um espaço de trabalho – agente [Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) para o agente Linux.  Certifique-se de selecionar a seqüência completa no portal Azure e copiá-la e colá-la cuidadosamente.

##### <a name="configuration-not-downloaded"></a>Configuração não baixada

Sua conta de espaço de trabalho e automação do Log Analytics deve estar em uma região vinculada. Para obter uma lista de regiões suportadas, consulte mapeamentos do [espaço de trabalho Azure Automation e Log Analytics](../how-to/region-mappings.md).

Você também pode precisar atualizar a data e/ou fuso horário do seu computador. Se você selecionar um intervalo de tempo personalizado, certifique-se de que o intervalo está em UTC, o que pode ser diferente do fuso horário local.

## <a name="linux"></a>Linux

O Linux Hybrid Runbook Worker depende do [agente log analytics para Linux para](../../azure-monitor/platform/log-analytics-agent.md) se comunicar com sua conta de Automação para registrar o trabalhador, receber trabalhos de runbook e relatar status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Cenário: O agente log analytics para Linux não está sendo executado

#### <a name="issue"></a>Problema

O agente log analytics para Linux não está sendo executado

#### <a name="cause"></a>Causa

Se o agente não estiver sendo executado, ele impede que o Linux Hybrid Runbook Worker se comunique com a Azure Automation. O agente pode não estar fugindo por várias razões.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está `ps -ef | grep python`sendo executado inserindo o comando . Você deve ver saída semelhante à seguinte, os processos Python com a conta de usuário **nxautomation.** Se a solução Update Management ou Azure Automation não estiver ativada, nenhum dos seguintes processos será executado.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Todos eles estão localizados no **diretório /var/opt/microsoft/omsagent/state/automationworker/.**

* **oms.conf** - O processo de gerente de trabalho. Ele é iniciado diretamente do DSC.

* **worker.conf** - Processo de trabalhador híbrido autoregistrado. É iniciado pelo gerenciador do trabalhador. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Este processo não está presente se a solução de Gerenciamento de Atualizações não estiver ativada na máquina.

* **diy/worker.conf** - O processo de trabalhador híbrido DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele só difere do processo de trabalhador híbrido registrado automaticamente no detalhe-chave que ele usa uma configuração diferente. Esse processo não está presente se a solução Azure Automation estiver desativada e o DIY Linux Hybrid Worker não estiver registrado.

Se o agente não estiver em execução, execute `sudo /opt/microsoft/omsagent/bin/service_control restart`o seguinte comando para iniciar o serviço: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Cenário: A classe especificada não existe

Se você vir `The specified class does not exist..` o erro no **/var/opt/microsoft/omsconfig/omsconfig.log,** o agente Log Analytics para Linux precisa ser atualizado. Execute o seguinte comando para reinstalar o agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Windows Hybrid Runbook Worker depende do [agente log analytics do Windows para](../../azure-monitor/platform/log-analytics-agent.md) se comunicar com sua conta de Automação para registrar o trabalhador, receber trabalhos de runbook e relatar o status. Se o registro do trabalhador falhar, esta seção inclui alguns possíveis motivos.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Cenário: O agente de análise de log para Windows não está sendo executado

#### <a name="issue"></a>Problema

O `healthservice` não está funcionando na máquina Hybrid Runbook Worker.

#### <a name="cause"></a>Causa

Se o serviço Log Analytics for Windows não estiver sendo executado, o Hybrid Runbook Worker não poderá se comunicar com o Azure Automation.

#### <a name="resolution"></a>Resolução

Verifique se o agente está sendo executado inserindo o seguinte comando no PowerShell: `Get-Service healthservice`. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Cenário: Evento 4502 no registro do Gerente de Operações

#### <a name="issue"></a>Problema

No registro de eventos **Application and Services Logs\Operations Manager,** você vê `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` o evento 4502 e eventMessage que contém com a seguinte descrição:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Esse problema poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída a ***.azure-automation.net** na porta 443. 

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada trabalhador híbrido em **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Você pode verificar se há algum aviso ou erro nos logs de **aplicativos e serviços\Microsoft-SMA\Operations** and **Application and Services Logs\Operations\Operations(Logs\Operations)\Operations** Esses registros indicam uma conectividade ou outro tipo de problema que afeta o onboarding da função para a Automação Azure, ou um problema encontrado em operações normais. Para obter ajuda adicional para solucionar problemas com o agente Log Analytics, consulte [Problemas de solução de problemas com o agente Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Os trabalhadores híbridos enviam [saídas e mensagens](../automation-runbook-output-and-messages.md) do Runbook para o Azure Automation da mesma forma que os trabalhos de runbook em execução na nuvem enviam saídas e mensagens. Você pode habilitar os fluxos Verbose e Progress, assim como você faz para os runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Cenário: Orchestrator.Sandbox.exe não pode se conectar ao Office 365 através de proxy

#### <a name="issue"></a>Problema

Um script em execução em um Trabalhador de Runbook Híbrido do Windows não pode se conectar como esperado ao Office 365 em uma caixa de areia do Orchestrator. O script está usando [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) para conexão. 

Se você ajustar **Orchestrator.Sandbox.exe.config** para definir o proxy e a lista de desvio, a caixa de areia ainda não se conecta corretamente. Um arquivo **Powershell_ise.exe.config** com as mesmas configurações de proxy e lista de bypass parece funcionar como você espera. Os logs de Automação de Gerenciamento de Serviços (SMA) e os logs do PowerShell não fornecem nenhuma informação sobre proxy.

#### <a name="cause"></a>Causa

A conexão com o ADFS (Active Directory Federation Services, serviços de federação de diretórios ativos) no servidor não pode contornar o proxy. Lembre-se que uma caixa de areia PowerShell é executada como usuário conectado. No entanto, uma caixa de areia do Orchestrator é fortemente personalizada e pode ignorar as configurações do arquivo **Orchestrator.Sandbox.exe.config.** Ele tem código especial para manipulação de configurações de máquina ou proxy MMA, mas não para lidar com outras configurações de proxy personalizadas. 

#### <a name="resolution"></a>Resolução

Você pode resolver o problema para a caixa de areia do Orchestrator migrando seu script para usar os módulos Azure AD em vez do módulo MSOnline para cmdlets PowerShell. Consulte [Migração do Orquestrador para a Automação Azure (Beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Se você quiser continuar a usar os cmdlets do módulo MSOnline, altere seu script para usar [O Comando Invocado](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Especifique `ComputerName` `Credential` os valores para os parâmetros. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Essa alteração de código inicia uma sessão powershell totalmente nova sob o contexto das credenciais especificadas. Ele deve permitir que o tráfego flua através de um servidor proxy que está autenticando o usuário ativo.

>[!NOTE]
>Essa solução torna desnecessário manipular o arquivo de configuração da caixa de areia. Mesmo que você consiga fazer o arquivo de configuração funcionar com seu script, o arquivo é apagado cada vez que o agente Hybrid Runbook Worker é atualizado.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Cenário: Trabalhador híbrido do runbook não reportando

#### <a name="issue"></a>Problema

Sua máquina Hybrid Runbook Worker está em execução, mas você não verá dados de pulsação para a máquina no workspace.

A consulta de exemplo a seguir mostra as máquinas em um workspace e sua última pulsação:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado por um cache corrompido no Hybrid Runbook Worker.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, entre no Hybrid Runbook Worker e execute o seguinte script. Esse script interrompe o agente Log Analytics para Windows, remove seu cache e reinicia o serviço. Essa ação força o Hybrid Runbook Worker baixar novamente sua configuração da automação do Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: Você não pode adicionar um trabalhador híbrido de runbook

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao tentar adicionar um Hybrid Runbook Worker usando o cmdlet `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado se a máquina já estiver registrada em uma conta de Automação diferente ou se você tentar ler o Hybrid Runbook Worker depois de removê-lo de uma máquina.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, remova a `HealthService`seguinte tecla `Add-HybridRunbookWorker` de registro, reinicie o e tente o cmdlet novamente.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.