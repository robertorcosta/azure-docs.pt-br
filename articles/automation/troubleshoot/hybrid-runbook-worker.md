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
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190969"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de trabalhadores de runbooks híbridos

Este artigo fornece informações sobre como solucionar problemas com os trabalhadores de runbook híbridos.

## <a name="general"></a>Geral

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, recebe trabalhos de runbook e status de relatórios. Para o Windows, esse agente é o agente Log Analytics para Windows, também conhecido como Microsoft Monitoring Agent (MMA). Para o Linux, é o agente Log Analytics para Linux.

### <a name="runbook-execution-fails"></a>Cenário: a execução do Runbook falha

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe o erro a seguir.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Seu runbook será suspenso logo depois de tentar executar três vezes. Há condições que podem interromper a conclusão do runbook. A mensagem de erro relacionada pode não incluir nenhuma informação adicional.

#### <a name="cause"></a>Causa

Estas são as possíveis causas:

* Os runbooks não podem se autenticar com recursos locais.

* O Hybrid Worker está usando um proxy ou firewall.

* O computador configurado para executar o recurso de Hybrid Runbook Worker não atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída para *. azure-automation.net na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de o trabalho ser configurado para hospedar esse recurso. Os Runbooks e o processo em segundo plano que eles usam podem fazer com que o sistema seja usado em excesso e causar atrasos ou tempos limite de trabalho do runbook.

Confirme se o computador para executar o recurso de Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore o uso de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Qualquer pressão de memória ou CPU pode indicar a necessidade de atualizar os recursos. Você também pode selecionar um recurso de computação diferente que dê suporte aos requisitos mínimos e dimensionamento quando as demandas de carga de trabalho indicarem que um aumento é necessário.

Verifique o log de eventos do **Microsoft SMA** para ver um evento correspondente com descrição *Processo Win32 Encerrado com o código [4294967295]* . A causa desse erro é que você não configurou a autenticação em seus runbooks ou especificou as credenciais executar como para o grupo de Hybrid Runbook Worker. Examine as permissões de runbook em [executando runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md) para confirmar que você configurou corretamente a autenticação para seus runbooks.

### <a name="no-cert-found"></a>Cenário: nenhum certificado foi encontrado no repositório de certificados no Hybrid Runbook Worker

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

Esse erro ocorre quando você tenta usar uma [conta Executar como](../manage-runas-account.md) em um runbook que é executado em um Hybrid runbook Worker em que o certificado da conta Executar como não está presente. Hybrid runbook Workers não têm o ativo de certificado localmente por padrão, o que é exigido pela conta Executar como para operar corretamente.

#### <a name="resolution"></a>Resolução

Se o seu Hybrid Runbook Worker for uma VM do Azure, você poderá usar [identidades gerenciadas para recursos do Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez disso. Esse cenário simplifica a autenticação, permitindo que você se autentique em recursos do Azure usando a identidade gerenciada da VM do Azure em vez da conta Executar como. Quando o Hybrid Runbook Worker é um computador local, você precisa instalar o certificado da conta Executar como no computador. Para saber como instalar o certificado, consulte as etapas para executar o runbook Export-RunAsCertificateToHybridWorker do PowerShell em [executando runbooks em um Hybrid runbook Worker](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Cenário: erro 403 durante o registro de Hybrid Runbook Worker

#### <a name="issue"></a>Problema

A fase de registro inicial do trabalho falha e você recebe o seguinte erro (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Causa

Estas são as possíveis causas:
* Há uma ID de espaço de trabalho digitada Inou chave de espaço de trabalho (primária) insuficiente nas configurações do agente. 
* O Hybrid Runbook Worker não pode baixar a configuração, causando um erro de vinculação de conta. Quando o Azure habilita soluções, ele dá suporte a apenas determinadas regiões para vincular um Log Analytics espaço de trabalho e uma conta de automação. Também é possível que uma data e/ou hora incorreta esteja definida no computador. Se o tempo for +/-15 minutos a partir da hora atual, a integração falhará.

#### <a name="resolution"></a>Resolução

##### <a name="mistyped-workspace-idkey"></a>ID/chave do espaço de trabalho digitado
Para verificar se a ID do espaço de trabalho ou a chave do espaço de trabalho do agente foi digitada [inalgumamente, consulte Adicionando ou removendo um espaço de trabalho –](../../azure-monitor/platform/agent-manage.md#windows-agent) agente do Windows para o agente do Windows ou [adicionando ou removendo um agente do espaço de trabalho – Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) para o agente Linux.  Certifique-se de selecionar a cadeia de caracteres completa do portal do Azure e copie e cole-a com cuidado.

##### <a name="configuration-not-downloaded"></a>Configuração não baixada

Seu espaço de trabalho do Log Analytics e a conta de automação devem estar em uma região vinculada. Para obter uma lista de regiões com suporte, confira [automação do Azure e mapeamentos de espaço de trabalho do log Analytics](../how-to/region-mappings.md).

Talvez você também precise atualizar a data e o fuso horário do seu computador. Se você selecionar um intervalo de tempo personalizado, verifique se o intervalo está em UTC, que pode ser diferente do seu fuso horário local.

## <a name="linux"></a>Linux

O Hybrid Runbook Worker do Linux depende do [agente de log Analytics para Linux](../../azure-monitor/platform/log-analytics-agent.md) para se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="oms-agent-not-running"></a>Cenário: o agente do Log Analytics para Linux não está em execução

#### <a name="issue"></a>Problema

O agente do Log Analytics para Linux não está em execução

#### <a name="cause"></a>Causa

Se o agente não estiver em execução, ele impedirá que o Hybrid Runbook Worker Linux se comunique com a automação do Azure. O agente pode não estar em execução por vários motivos.

#### <a name="resolution"></a>Resolução

 Verifique se o agente está em execução digitando o seguinte comando: `ps -ef | grep python`. Você deverá ver uma saída semelhante à seguinte, os processos de python com conta de usuário **nxautomation**. Se as soluções de Gerenciamento de Atualizações ou de Automação do Azure não estiverem ativadas, nenhum dos processos a seguir estará em execução.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão todos localizados no diretório `/var/opt/microsoft/omsagent/state/automationworker/`.

* **OMS. conf** -o processo do Gerenciador de trabalho. Ele é iniciado diretamente do DSC.

* **Worker. conf** -o processo de trabalho híbrido registrado automaticamente. É iniciado pelo gerenciador do trabalhador. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Este processo não está presente se a solução de Gerenciamento de Atualizações não estiver ativada na máquina.

* **DIY/Worker. conf** -o processo de trabalho híbrido do DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele difere apenas do processo de trabalho híbrido registrado automaticamente, no detalhe da chave, que ele usa uma configuração diferente. Esse processo não estará presente se a solução de automação do Azure estiver desabilitada e o Hybrid Worker DIY do Linux não estiver registrado.

Se o agente não estiver em execução, execute o seguinte comando para iniciar o serviço: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Cenário: a classe especificada não existe

Se você vir o erro **a classe especificada não existe.** no `/var/opt/microsoft/omsconfig/omsconfig.log`, o agente do Log Analytics para Linux precisa ser atualizado. Execute o seguinte comando para reinstalar o agente:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

O Windows Hybrid Runbook Worker depende do [agente de log Analytics para Windows](../../azure-monitor/platform/log-analytics-agent.md) se comunicar com sua conta de automação para registrar o trabalho, receber trabalhos de runbook e relatar o status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="mma-not-running"></a>Cenário: o Microsoft Monitoring Agent não está em execução

#### <a name="issue"></a>Problema

O serviço `healthservice` não está sendo executado na máquina Hybrid Runbook Worker.

#### <a name="cause"></a>Causa

Se o serviço da Microsoft Monitoring Agent Microsoft não estiver em execução, esse estado impedirá que o Hybrid Runbook Worker se comunique com a automação do Azure.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em execução digitando o seguinte comando no PowerShell: `Get-Service healthservice`. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="event-4502"></a>Cenário: evento 4502 no log Operations Manager

#### <a name="issue"></a>Problema

No log de eventos **Application and Services logs Manager** , você vê o evento 4502 e o eventMessage que contém **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** com a seguinte descrição: *o certificado apresentado pelo serviço \<WSID\>. OMS.opinsights.Azure.com não foi emitido por uma autoridade de certificação usada para os serviços da Microsoft. Entre em contato com seu administrador de rede para ver se eles estão executando um proxy que intercepta a comunicação TLS/SSL.*

#### <a name="cause"></a>Causa

Esse problema poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída para *.azure-automation.net na porta 443. 

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum evento de aviso ou de erro no log de eventos **Application and Services Logs\Microsoft-SMA\Operations** e **Application and Services logs Manager** que indicam uma conectividade ou outro problema que afeta a integração da função com a automação do Azure ou com o problema em operações normais. Para obter ajuda adicional sobre como solucionar problemas com o agente de Log Analytics, consulte [solucionar problemas com o log Analytics agente do Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

[A saída e as mensagens do runbook](../automation-runbook-output-and-messages.md) são enviadas à Automação do Azure do Hybrid Workers assim como os trabalhos do runbook são executados na nuvem. Também é possível habilitar os fluxos Verbose e Progress da mesma forma que você faria para outros runbooks.

### <a name="corrupt-cache"></a>Cenário: Hybrid Runbook Worker não relatório

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

Para resolver esse problema, entre no Hybrid Runbook Worker e execute o seguinte script. Esse script interrompe o Microsoft Monitoring Agent, remove seu cache e reinicia o serviço. Essa ação força o Hybrid Runbook Worker a baixar novamente sua configuração da automação do Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Cenário: não é possível adicionar um Hybrid Runbook Worker

#### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao tentar adicionar um Hybrid Runbook Worker usando o cmdlet `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Causa

Esse problema pode ser causado se o computador já estiver registrado com uma conta de automação diferente ou se você tentar ler o Hybrid Runbook Worker depois de removê-lo de um computador.

#### <a name="resolution"></a>Resolução

Para resolver esse problema, remova a seguinte chave do Registro, reinicie `HealthService` e experimente o cmdlet `Add-HybridRunbookWorker` novamente:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.