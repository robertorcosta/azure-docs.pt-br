---
title: Solução de problemas - Trabalhadores do Runbook Híbrido de Automação do Azure
description: Este artigo fornece informações sobre solução de problemas de trabalhadores do Runbook Híbrido de Automação do Azure
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 33e3e162892f1e2a148258273160ca26fa9c2efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153515"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de trabalhadores de runbooks híbridos

Este artigo fornece informações sobre como solucionar problemas com os trabalhadores de runbook híbridos.

## <a name="general"></a>Geral

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, recebe trabalhos de runbook e status de relatórios. Para o Windows, este agente é o agente log analytics para Windows, também referido como O Microsoft Monitoring Agent (MMA). Para Linux, é o agente log analytics para Linux.

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

* O computador configurado para executar o recurso Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

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
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta usar uma [conta Run As](../manage-runas-account.md) em um runbook que é executado em um Trabalhador de runbook híbrido onde o certificado de conta Run As não está presente. Os trabalhadores do runbook híbrido não têm o ativo de certificado localmente por padrão, o que é exigido pela conta Run As para operar corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Hybrid Runbook Worker for uma VM Do Azure, você poderá usar [identidades gerenciadas para recursos do Azure.](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) Esse cenário simplifica a autenticação, permitindo que você se autentique nos recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Run As. Quando o Hybrid Runbook Worker é uma máquina no local, você precisa instalar o certificado de conta Run As na máquina. Para saber como instalar o certificado, consulte as etapas para executar o runbook PowerShell Export-RunAsCertificateToHybridWorker em [execução de runbooks em um Trabalhador de runbook híbrido](../automation-hrw-run-runbooks.md).

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

Você também pode precisar atualizar a data e ou fuso horário do seu computador. Se você selecionar um intervalo de tempo personalizado, certifique-se de que o intervalo está em UTC, o que pode ser diferente do fuso horário local.

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

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>Cenário: O Microsoft Monitoring Agent não está executando

#### <a name="issue"></a>Problema

O serviço `healthservice` não está sendo executado na máquina Hybrid Runbook Worker.

#### <a name="cause"></a>Causa

Se o serviço do Microsoft Monitoring Agent não estiver sendo executado, o Hybrid Runbook Worker será impedido de se comunicar com a Azure Automation.

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

Para resolver esse problema, entre no Hybrid Runbook Worker e execute o seguinte script. Esse script interrompe o Microsoft Monitoring Agent, remove seu cache e reinicia o serviço. Essa ação força o Hybrid Runbook Worker baixar novamente sua configuração da automação do Azure.

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

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.