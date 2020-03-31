---
title: Solução de problemas com o Controle de Alterações do Azure
description: Saiba como solucionar e resolver problemas com o recurso De rastreamento e inventário de alterações de automação do Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198523"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Solucionar problemas do Controle de Alterações e do Inventário

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: Os registros de rastreamento de alterações não estão sendo exibidos para máquinas Windows

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de rastreamento de alteração ou inventário para máquinas Windows que estejam a bordo para rastreamento de alterações.

#### <a name="cause"></a>Causa

Este erro pode ter as seguintes causas:

* O Microsoft Monitoring Agent não está funcionando.
* A comunicação de volta à conta de Automação está sendo bloqueada.
* Os pacotes de gerenciamento para rastreamento de alterações não são baixados.
* O VM que está sendo a bordo pode ter vindo de uma máquina clonada que não estava preparada com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

As soluções descritas abaixo podem ajudar a resolver seu problema. Se você ainda precisar de ajuda, você pode coletar informações de diagnóstico e suporte de contato. Na máquina do agente, navegue até C:\Arquivos do programa\Microsoft Monitoring Agent\Agent\Tools e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por padrão, o rastreamento de erros está ativado. Para habilitar mensagens de erro verbose como no exemplo anterior, use o parâmetro *VER.* Para obter informações, use *INF* ao invocar **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent não está em execução

Verifique se o Microsoft Monitoring Agent (HealthService.exe) está sendo executado na máquina.

##### <a name="communication-to-automation-account-blocked"></a>Conta de comunicação para automação bloqueada

Verifique o Visualizador de Eventos na máquina e procure por quaisquer eventos que tenham a palavra "changetracking" neles.

Consulte [Automatizar recursos em seu data center ou nuvem usando o Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) para aprender sobre endereços e portas que devem ser permitidas para que o Rastreamento de Alterações funcione.

##### <a name="management-packs-not-downloaded"></a>Pacotes de gerenciamento não baixados

Verifique se os seguintes pacotes de gerenciamento de rastreamento e inventário de alterações estão instalados localmente:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM de máquina clonada que não foi sysprepped

Se estiver usando uma imagem clonada, sysprep a imagem primeiro e, em seguida, instale o Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Cenário: Sem resultados de rastreamento ou inventário de alterações em máquinas Linux

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de rastreamento de inventário ou alteração para máquinas Linux que estejam a bordo para rastreamento de alterações. 

#### <a name="cause"></a>Causa
Aqui estão possíveis causas específicas para este problema:
* O agente log analytics para Linux não está sendo executado.
* O agente log analytics para Linux não está configurado corretamente.
* Existem conflitos de Monitoramento de Integridade de Arquivos (FIM).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente de Log Analytics para Linux não executando

Verifique se o daemon para o agente Log Analytics para Linux (omsagent) está sendo executado em sua máquina. Execute a seguinte consulta no espaço de trabalho do Log Analytics vinculado à sua conta de Automação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se você não ver sua máquina em resultados de consulta, ela não fez check-in recentemente. Provavelmente há um problema de configuração local e você deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [Coletar dados de log com o agente Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Se a máquina aparecer nos resultados da consulta, verifique a configuração do escopo. Consulte [Direcionar soluções de monitoramento no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para obter mais solução de problemas deste problema, consulte [Issue: Você não está vendo nenhum dado do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente de Análise de Log para Linux não configurado corretamente

O agente Log Analytics para Linux pode não estar configurado corretamente para a coleta de saída de log e linha de comando usando a ferramenta OMS Log Collector. Consulte [Acompanhar alterações no ambiente com a solução 'Alterar rastreamento'.](../change-tracking.md)

##### <a name="fim-conflicts"></a>Conflitos FIM

O recurso FIM do Azure Security Center pode estar validando incorretamente a integridade de seus arquivos Linux. Verifique se a FIM está operacional e configurada corretamente para monitoramento de arquivos Linux. Consulte [Acompanhar alterações no ambiente com a solução 'Alterar rastreamento'.](../change-tracking.md)

## <a name="next-steps"></a>Próximas etapas

Se você não vê o seu problema ou não consegue resolver o seu problema, use um dos seguintes canais para obter mais suporte.

* Obtenha respostas de especialistas do Azure através [do Azure Forums](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.
