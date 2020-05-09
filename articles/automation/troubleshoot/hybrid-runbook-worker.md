---
title: Solucionando problemas de Hybrid runbook Workers da automação do Azure
description: Este artigo fornece informações para solucionar problemas de Hybrid runbook Workers da automação do Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d734c910cc966cfd83f1e1c7f9cbd728643fbc4
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836505"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de trabalhadores de runbooks híbridos

Este artigo fornece informações sobre como solucionar problemas com Hybrid runbook Workers da automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação do Azure, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="general"></a>Geral

A Hybrid Runbook Worker depende de um agente para se comunicar com sua conta de automação do Azure para registrar o trabalho, receber trabalhos de runbook e relatar o status. Para o Windows, esse agente é o agente Log Analytics para Windows. Para o Linux, é o agente Log Analytics para Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Cenário: a execução do Runbook falha

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe a seguinte mensagem de erro:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Seu runbook será suspenso logo depois de tentar executar três vezes. Há condições que podem interromper a conclusão do runbook. A mensagem de erro relacionada pode não incluir nenhuma informação adicional.

#### <a name="cause"></a>Causa

Estas são as possíveis causas:

* Os runbooks não podem se autenticar com recursos locais.
* O Hybrid Worker está usando um proxy ou firewall.
* O computador configurado para executar o Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída para ***. Azure-Automation.net** na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de o trabalho ser configurado para hospedar esse recurso. Os Runbooks e o processo em segundo plano que eles usam podem fazer com que o sistema seja superutilizado e causar atrasos ou tempos limite de trabalho do runbook.

Confirme se o computador para executar o recurso de Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore o uso de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Qualquer pressão de memória ou CPU pode indicar a necessidade de atualizar os recursos. Você também pode selecionar um recurso de computação diferente que dê suporte aos requisitos mínimos e dimensionamento quando as demandas de carga de trabalho indicarem que um aumento é necessário.

Verifique o log de eventos do **sma da Microsoft** para obter um evento correspondente `Win32 Process Exited with code [4294967295]`com a descrição. A causa desse erro é que você não configurou a autenticação em seus runbooks ou especificou as credenciais executar como para o grupo de Hybrid Runbook Worker. Examine as permissões de runbook em [executando runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md) para confirmar que você configurou corretamente a autenticação para seus runbooks.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Cenário: evento 15011 no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

O Hybrid Runbook Worker recebe o evento 15011, indicando que um resultado da consulta não é válido. O erro a seguir é exibido quando o trabalho tenta abrir uma conexão com o [servidor signalr](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Causa

A Hybrid Runbook Worker não foi configurada corretamente para a solução de implantação automatizada. Essa solução contém uma parte que conecta a VM ao espaço de trabalho Log Analytics. O script do PowerShell procura o espaço de trabalho na assinatura com o nome fornecido. Nesse caso, o espaço de trabalho Log Analytics está em uma assinatura diferente. O script não pode localizar o espaço de trabalho e tenta criar um, mas o nome já está em uso. Como resultado, a implantação falha.

#### <a name="resolution"></a>Resolução

Você tem duas opções para resolver esse problema:

* Modifique o script do PowerShell para procurar o espaço de trabalho Log Analytics em outra assinatura. Essa é uma boa solução se você planeja implantar vários computadores Hybrid Runbook Worker no futuro.

* Configure manualmente a máquina de trabalho para ser executada em uma área restrita do Orchestrator. Em seguida, execute um runbook criado na conta de automação do Azure no trabalho para testar a funcionalidade.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Cenário: VMs do Windows Azure descartadas automaticamente de um grupo do Hybrid Worker

#### <a name="issue"></a>Problema

Você não pode ver a Hybrid Runbook Worker ou VMs quando a máquina de trabalho foi desativada por muito tempo.

#### <a name="cause"></a>Causa

A máquina Hybrid Runbook Worker não tem pingado na automação do Azure por mais de 30 dias. Como resultado, a automação limpou o grupo de Hybrid Runbook Worker ou o grupo de trabalho do sistema. 

#### <a name="resolution"></a>Resolução

Inicie o computador de trabalho e, em seguida, rereregister-o com a automação do Azure. Para obter instruções sobre como instalar o ambiente de runbook e conectar-se à automação do Azure, consulte [implantar um Hybrid runbook Worker do Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Cenário: nenhum certificado foi encontrado no repositório de certificados no Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Um runbook em execução em um Hybrid Runbook Worker falha com a seguinte mensagem de erro:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Causa

Esse erro ocorre quando você tenta usar uma [conta Executar como](../manage-runas-account.md) em um runbook que é executado em um Hybrid runbook Worker em que o certificado da conta Executar como não está presente. Hybrid runbook Workers não têm o ativo de certificado localmente por padrão. A conta Executar como requer que este ativo opere corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Hybrid Runbook Worker for uma VM do Azure, você poderá usar a [autenticação de runbook com identidades gerenciadas](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez disso. Esse cenário simplifica a autenticação, permitindo que você se autentique em recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Executar como. Quando o Hybrid Runbook Worker é um computador local, você precisa instalar o certificado da conta Executar como no computador. Para saber como instalar o certificado, consulte as etapas para executar o runbook **Export-RunAsCertificateToHybridWorker** do PowerShell em [executar runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Cenário: erro 403 durante o registro de um Hybrid Runbook Worker

#### <a name="issue"></a>Problema

A fase de registro inicial do trabalho falha e você recebe o seguinte erro (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Os seguintes problemas são possíveis causas:

* Há uma ID de espaço de trabalho digitada Inou chave de espaço de trabalho (primária) insuficiente nas configurações do agente. 
* O Hybrid Runbook Worker não pode baixar a configuração, o que causa um erro de vinculação de conta. Quando o Azure habilita soluções, ele dá suporte a apenas determinadas regiões para vincular um Log Analytics espaço de trabalho e uma conta de automação. Também é possível que uma data ou hora incorreta esteja definida no computador. Se o tempo for mais ou menos 15 minutos a partir da hora atual, a integração falhará.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-id-or-key"></a>ID ou chave do espaço de trabalho digitada Inou
Para verificar se a ID do espaço de trabalho do agente ou a chave do espaço de trabalho foi digitada incomo, consulte [adicionando ou removendo um espaço de trabalho –](../../azure-monitor/platform/agent-manage.md#windows-agent) agente do Windows para o agente do Windows ou [adicionando ou removendo um agente do espaço de trabalho – Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) para o agente Linux. Certifique-se de selecionar a cadeia de caracteres completa do portal do Azure, e copie e cole-a com cuidado.

##### <a name="configuration-not-downloaded"></a>Configuração não baixada

Seu espaço de trabalho do Log Analytics e a conta de automação devem estar em uma região vinculada. Para obter uma lista de regiões com suporte, confira [automação do Azure e mapeamentos de espaço de trabalho do log Analytics](../how-to/region-mappings.md).

Talvez você também precise atualizar a data ou o fuso horário do seu computador. Se você selecionar um intervalo de tempo personalizado, verifique se o intervalo está em UTC, que pode ser diferente do seu fuso horário local.

## <a name="linux"></a>Linux

O Hybrid Runbook Worker do Linux depende do [agente de log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md) para se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Cenário: o Linux Hybrid Runbook Worker recebe uma solicitação para uma senha ao assinar um runbook

#### <a name="issue"></a>Problema

A execução `sudo` do comando para um Hybrid runbook Worker Linux recupera uma solicitação inesperada para uma senha.

#### <a name="cause"></a>Causa

A conta **nxautomationuser** para o agente de log Analytics para Linux não está configurada corretamente no arquivo **sudoers** . O Hybrid Runbook Worker precisa da configuração apropriada de permissões de conta e outros dados para que ele possa assinar runbooks no runbook Worker do Linux.

#### <a name="resolution"></a>Resolução

* Verifique se o Hybrid Runbook Worker tem o executável GnuPG (GPG) no computador.

* Verifique a configuração da conta **nxautomationuser** no arquivo **sudoers** . Consulte [executando runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Cenário: o agente do Log Analytics para Linux não está em execução

#### <a name="issue"></a>Problema

O agente do Log Analytics para Linux não está em execução.

#### <a name="cause"></a>Causa

Se o agente não estiver em execução, ele impedirá que o Hybrid Runbook Worker Linux se comunique com a automação do Azure. O agente pode não estar em execução por vários motivos.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está em execução digitando `ps -ef | grep python`o comando. Você deve ver saídas semelhantes às seguintes. Os processos do Python com a conta de usuário **nxautomation** . Se a solução Gerenciamento de Atualizações ou de automação do Azure não estiver habilitada, nenhum dos processos a seguir será executado.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão todos localizados no diretório/var/opt/Microsoft/omsagent/State/automationworker/.

* **OMS. conf**: o processo do Gerenciador de trabalho. Ele é iniciado diretamente do DSC.
* **Worker. conf**: o processo de trabalho híbrido registrado automaticamente. É iniciado pelo gerenciador do trabalhador. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Este processo não está presente se a solução de Gerenciamento de Atualizações não estiver ativada na máquina.
* **DIY/Worker. conf**: o processo de trabalho híbrido DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele difere apenas do processo de trabalho híbrido registrado automaticamente no detalhe da chave que ele usa uma configuração diferente. Esse processo não estará presente se a solução de automação do Azure estiver desabilitada e o Hybrid Worker DIY do Linux não estiver registrado.

Se o agente não estiver em execução, execute o seguinte comando para iniciar o `sudo /opt/microsoft/omsagent/bin/service_control restart`serviço:.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Cenário: a classe especificada não existe

Se você vir a mensagem `The specified class does not exist..` de erro no **/var/opt/Microsoft/omsconfig/omsconfig.log**, o agente do log Analytics para Linux precisará ser atualizado. Execute o comando a seguir para reinstalar o agente.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Windows Hybrid Runbook Worker depende do [agente de log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md) se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalho falhar, esta seção incluirá alguns motivos possíveis.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Cenário: o agente do Log Analytics para Windows não está em execução

#### <a name="issue"></a>Problema

O `healthservice` não está em execução na máquina Hybrid runbook Worker.

#### <a name="cause"></a>Causa

Se o Log Analytics para o serviço do Windows não estiver em execução, o Hybrid Runbook Worker não poderá se comunicar com a automação do Azure.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em execução digitando o seguinte comando no PowerShell `Get-Service healthservice`:. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Cenário: evento 4502 no log de Operations Manager

#### <a name="issue"></a>Problema

No log de eventos **Application and Services logs Manager** , você vê o evento 4502 e uma mensagem de evento `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` que contém com a seguinte descrição:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Causa

Esse problema poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída para ***. Azure-Automation.net** na porta 443.

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum evento de aviso ou de erro nos logs de eventos **Application and Services Logs\Microsoft-SMA\Operations** e **Application and Services logs Manager** . Esses logs indicam uma conectividade ou outro tipo de problema que afeta a integração da função à automação do Azure ou um problema encontrado em operações normais. Para obter ajuda adicional sobre como solucionar problemas com o agente de Log Analytics, consulte [solucionar problemas com o log Analytics agente do Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Os Hybrid Workers enviam a [saída do runbook e as mensagens](../automation-runbook-output-and-messages.md) para a automação do Azure da mesma forma que os trabalhos de runbook em execução na nuvem enviam a saída e as mensagens. Você pode habilitar os fluxos detalhados e de progresso da mesma forma como faz para runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Cenário: Orchestrator. sandbox. exe não pode se conectar ao Office 365 por meio do proxy

#### <a name="issue"></a>Problema

Um script em execução em um Hybrid Runbook Worker do Windows não pode se conectar conforme o esperado para o Office 365 em uma área restrita do Orchestrator. O script está usando [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) para conexão. 

Se você ajustar **Orchestrator. sandbox. exe. config** para definir o proxy e a lista de bypass, a área restrita ainda não se conectará corretamente. Um arquivo **Powershell_ise. exe. config** com as mesmas configurações de lista de proxies e bypass parece funcionar conforme o esperado. Os logs do Service Management Automation (SMA) e os logs do PowerShell não fornecem nenhuma informação sobre o proxy.

#### <a name="cause"></a>Causa

A conexão com Serviços de Federação do Active Directory (AD FS) (AD FS) no servidor não pode ignorar o proxy. Lembre-se de que uma área restrita do PowerShell é executada como o usuário registrado. No entanto, uma área restrita do Orchestrator é bastante personalizada e pode ignorar as configurações do arquivo **Orchestrator. sandbox. exe. config** . Ele tem um código especial para lidar com configurações de proxy do computador ou do Log Analytics Agent, mas não para lidar com outras configurações de proxy personalizadas. 

#### <a name="resolution"></a>Resolução

Você pode resolver o problema para a área restrita do Orchestrator migrando seu script para usar os módulos Azure Active Directory em vez do módulo MSOnline para cmdlets do PowerShell. Para obter mais informações, consulte [migrando do Orchestrator para a automação do Azure (beta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Se você quiser continuar a usar os cmdlets do módulo MSOnline, altere seu script para usar [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Especifique valores para os `ComputerName` parâmetros `Credential` e. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Essa alteração de código inicia uma sessão totalmente nova do PowerShell no contexto das credenciais especificadas. Ele deve permitir que o tráfego flua por meio de um servidor proxy que está Autenticando o usuário ativo.

>[!NOTE]
>Essa solução torna desnecessário manipular o arquivo de configuração da área restrita. Mesmo que você tenha sucesso em fazer com que o arquivo de configuração funcione com o script, o arquivo será apagado sempre que o agente de Hybrid Runbook Worker for atualizado.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Cenário: Hybrid Runbook Worker não relatório

#### <a name="issue"></a>Problema

Sua máquina Hybrid Runbook Worker está em execução, mas você não vê dados de pulsação para o computador no espaço de trabalho.

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

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Cenário: não é possível adicionar um Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem ao tentar adicionar um Hybrid Runbook Worker usando o `Add-HybridRunbookWorker` cmdlet:

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado se o computador já estiver registrado com uma conta de automação diferente ou se você tentar adicionar novamente o Hybrid Runbook Worker depois de removê-lo de um computador.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, remova a seguinte chave do registro, `HealthService`reinicie e tente `Add-HybridRunbookWorker` o cmdlet novamente.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não puder resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente. O suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.
