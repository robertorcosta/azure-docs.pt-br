---
title: Solução de problemas problemas do agente de atualização do Windows no Gerenciamento de Atualização de Automação do Azure
description: Saiba como solucionar problemas e resolver problemas com o agente de atualização do Windows usando a solução Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678982"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Solucionar problemas do agente de atualização do Windows

Pode haver muitas razões pelas quais sua máquina não está aparecendo tão pronta (saudável) no Gerenciamento de Atualizações. No Update Management, você pode verificar a saúde de um agente Híbrido Runbook Worker para determinar o problema subjacente. Este artigo discute como executar o solucionador de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline).

A seguir estão os três estados de prontidão para uma máquina:

* Pronto - O Híbrido Runbook Worker foi implantado e foi visto pela última vez há menos de 1 hora.
* Desconectado - O Híbrido Runbook Worker foi implantado e foi visto pela última vez há mais de 1 hora.
* Não configurado - O Hybrid Runbook Worker não foi encontrado ou não terminou o onboarding.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, ao clicar no link **Solução de Problemas** na coluna **Preparação do Agente de Atualização** no portal, a página Solucionar Problemas do Agente de Atualização será iniciada. Para máquinas não-Azure, o link traz você para este artigo. Consulte as [instruções off-line](#troubleshoot-offline) para solucionar problemas de uma máquina não-Azure.

![Atualizar lista de gerenciamento de máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a saúde do Trabalhador híbrido, a VM deve estar funcionando. Se a VM não estiver em execução, será exibido o botão **Iniciar a VM**.

Sobre o solucionar problemas de atualização de agente página, selecione **executar verificações de** para iniciar a solução de problemas. O solucionador de problemas usa [o Comando executar](../../virtual-machines/windows/run-command.md) para executar um script na máquina para verificar dependências. Quando a solução de problemas é concluída, ela retorna o resultado das verificações.

![Solucionar problemas de agente de atualização de página](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são mostrados na página quando estão prontos. As seções de verificações mostram o que está incluído em cada verificação.

![Solucionar problemas de verificações do agente de atualização](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2012 e posterior |.NET Framework 4.6 ou posterior é necessário. ([Fazer o download do .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> O Windows PowerShell 5.1 é necessário.  ([Faça o download do Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

A verificação .NET Framework verifica se o sistema tem um mínimo de [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação WMF verifica se o sistema tem a versão necessária do Windows Management Framework (WMF) - [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Essa verificação determina se você está usando o TLS 1.2 para criptografar suas comunicações. O TLS 1.0 não é mais suportado pela plataforma. Recomendamos que os clientes usem o TLS 1.2 para se comunicar com o Gerenciamento de Atualizações.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o agente pode se comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, consulte [O planejamento de rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o Serviço de dados do runtime da tarefa.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Runtime do Trabalho. Para obter uma lista de endereços e portas a serem abertas, consulte [O planejamento de rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Verificações de integridade do serviço de VM

### <a name="monitoring-agent-service-status"></a>Monitoramento de status de serviço do agente

Esta verificação determina se o agente`healthservice`log analytics para Windows ( ) está sendo executado na máquina. Para saber mais sobre como solucionar problemas no serviço, consulte [o agente Log Analytics para Windows que não está sendo executado](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o agente Log Analytics para Windows, consulte [Instalar e configurar o agente Log Analytics para Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoramento de eventos de serviço de agente

Esta verificação determina se algum evento 4502 aparecerá no log do Azure Operations Manager na máquina nas últimas 24 horas.

Para saber mais sobre esse evento, confira o [guia de solução de problemas](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

### <a name="machinekeys-folder-access"></a>Acesso à pasta MachineKeys

A verificação de acesso à pasta Crypto determina se a conta do sistema local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Solução de problemas offline

Você pode usar o solucionador de problemas em um Hybrid Runbook Worker offline, executando o script localmente. Você pode obter o script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), na Galeria do PowerShell. Você deve ter o WMF 4.0, ou superior, instalado para executar o script. Para baixar a versão mais recente do PowerShell, consulte [Instalando várias versões do PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A saída deste script se parece com o seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Próximas etapas

Para solucionar mais problemas com os trabalhadores de runbook híbridos, consulte [Solucionar problemas de trabalhadores de runbook híbridos](hybrid-runbook-worker.md).
