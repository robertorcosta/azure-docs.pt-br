---
title: Problemas de solução de problemas com rastreamento e inventário de alterações
description: Saiba como solucionar e resolver problemas com a solução de rastreamento e inventário de alterações de automação do Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679342"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemas de rastreamento de alterações e problemas de inventário

Este artigo descreve como solucionar problemas de rastreamento de alterações e problemas de inventário.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Os registros de rastreamento e inventário de alterações não estão sendo exibidos para máquinas Windows

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de rastreamento e inventário de alterações para máquinas Windows que estejam a bordo.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O agente de análise de log para Windows não está sendo executado.
* A comunicação de volta à conta de Automação está sendo bloqueada.
* Os pacotes de gerenciamento de rastreamento e inventário de alterações não são baixados.
* O VM que está sendo a bordo pode ter vindo de uma máquina clonada que não estava preparada com o agente Log Analytics para Windows instalado.

#### <a name="resolution"></a>Resolução

Na máquina do agente Log Analytics, navegue até **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se você ainda precisar de ajuda, você pode coletar informações de diagnóstico e suporte de contato. 

> [!NOTE]
> O agente Log Analyticss habilita o rastreamento de erros por padrão. Para habilitar mensagens de erro verbose `VER` como no exemplo anterior, use o parâmetro. Para rastreamentos de informações, use `INF` ao invocar `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agente de análise de log para Windows que não está em execução

Verifique se o agente log analytics do Windows (**HealthService.exe)** está sendo executado na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Conta de comunicação para automação bloqueada

Verifique o Visualizador de Eventos na máquina e procure quaisquer eventos com a palavra `changetracking` presente.

Consulte [Automatizar recursos em seu data center ou nuvem usando o Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) para aprender sobre endereços e portas que devem ser permitidos para que o rastreamento de alterações e o inventário funcionem.

##### <a name="management-packs-not-downloaded"></a>Pacotes de gerenciamento não baixados

Verifique se os seguintes pacotes de gerenciamento de rastreamento e inventário de alterações estão instalados localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM de máquina clonada que não foi sysprepped

Se estiver usando uma imagem clonada, sysprep a imagem primeiro e, em seguida, instale o agente Log Analytics para Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Cenário: Sem resultados de rastreamento e inventário de alterações em máquinas Linux

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de rastreamento de inventário e alteração para máquinas Linux que estejam a bordo para a solução. 

#### <a name="cause"></a>Causa
Aqui estão possíveis causas específicas para este problema:
* O agente log analytics para Linux não está sendo executado.
* O agente log analytics para Linux não está configurado corretamente.
* Existem conflitos de Monitoramento de Integridade de Arquivos (FIM).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente de Log Analytics para Linux não executando

Verifique se o daemon para o agente Log Analytics para Linux (**omsagent)** está sendo executado em sua máquina. Execute a seguinte consulta no espaço de trabalho do Log Analytics vinculado à sua conta de Automação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se você não ver sua máquina em resultados de consulta, ela não fez check-in recentemente. Provavelmente há um problema de configuração local e você deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [Coletar dados de log com o agente Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Se a máquina aparecer nos resultados da consulta, verifique a configuração do escopo. Consulte [Direcionar soluções de monitoramento no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para obter mais problemas sobre este problema, consulte [Problema: Você não está vendo nenhum dado do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente de Análise de Log para Linux não configurado corretamente

O agente Log Analytics para Linux pode não estar configurado corretamente para a coleta de saída de log e linha de comando usando a ferramenta OMS Log Collector. Consulte [Rastrear alterações no ambiente com a solução 'Rastreamento e inventário de alterações'.](../change-tracking.md)

##### <a name="fim-conflicts"></a>Conflitos FIM

O recurso FIM do Azure Security Center pode estar validando incorretamente a integridade de seus arquivos Linux. Verifique se a FIM está operacional e configurada corretamente para monitoramento de arquivos Linux. Consulte [Rastrear alterações no ambiente com a solução 'Rastreamento e inventário de alterações'.](../change-tracking.md)

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema acima ou não consegue resolver o seu problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
