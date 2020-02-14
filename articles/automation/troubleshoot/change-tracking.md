---
title: Solução de problemas com o Controle de Alterações do Azure
description: Saiba como solucionar e resolver problemas com o Controle de Alterações de automação do Azure e o recurso de inventário.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198523"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Solucionar problemas do Controle de Alterações e do Inventário

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Cenário: registros de Controle de Alterações não estão sendo exibidos para computadores Windows

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de Controle de Alterações ou inventário para computadores Windows que são integrados para Controle de Alterações.

#### <a name="cause"></a>Causa

Esse erro pode ter as seguintes causas:

* O Microsoft Monitoring Agent não está em execução.
* A comunicação de volta com a conta de automação está sendo bloqueada.
* Os pacotes de gerenciamento para Controle de Alterações não são baixados.
* A VM que está sendo integrada pode ter vindo de um computador clonado que não foi Sysprep com o Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolução

As soluções descritas abaixo podem ajudar a resolver o problema. Se ainda precisar de ajuda, você poderá coletar informações de diagnóstico e contatar o suporte. No computador do agente, navegue até C:\Program Files\Microsoft Monitoring Agent\Agent\Tools e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Por padrão, o rastreamento de erros está habilitado. Para habilitar mensagens de erro detalhadas como no exemplo anterior, use o parâmetro *Ver* . Para rastreamentos de informações, use *inf* ao invocar **StartTracing. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent não está em execução

Verifique se o Microsoft Monitoring Agent (HealthService. exe) está em execução no computador.

##### <a name="communication-to-automation-account-blocked"></a>Comunicação com a conta de automação bloqueada

Verifique Visualizador de Eventos no computador e procure todos os eventos que tenham a palavra "ChangeTracking" neles.

Confira [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre endereços e portas que devem ser permitidos para que controle de alterações funcionem.

##### <a name="management-packs-not-downloaded"></a>Pacotes de gerenciamento não baixados

Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e inventário estão instalados localmente:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM do computador clonado que não foi sysprepdo

Se estiver usando uma imagem clonada, execute o Sysprep na imagem primeiro e, em seguida, instale o Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Cenário: nenhum resultado de Controle de Alterações ou inventário em computadores Linux

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de inventário ou Controle de Alterações para computadores Linux que são integrados para Controle de Alterações. 

#### <a name="cause"></a>Causa
Aqui estão as possíveis causas específicas para esse problema:
* O agente do Log Analytics para Linux não está em execução.
* O agente do Log Analytics para Linux não está configurado corretamente.
* Há conflitos de FIM (monitoramento de integridade de arquivo).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>O agente do Log Analytics para Linux não está em execução

Verifique se o daemon do agente de Log Analytics para Linux (omsagent) está em execução no seu computador. Execute a consulta a seguir no espaço de trabalho Log Analytics que está vinculado à sua conta de automação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente. Provavelmente, há um problema de configuração local e você deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [coletar dados de log com o agente de log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Se o computador aparecer nos resultados da consulta, verifique a configuração do escopo. Consulte [direcionando soluções de monitoramento no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para obter mais solução de problemas desse problema, consulte o [problema: você não está vendo nenhum dado do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente do Log Analytics para Linux não configurado corretamente

O agente do Log Analytics para Linux pode não estar configurado corretamente para a coleção de saída de linha de comando e de log usando a ferramenta do coletor de logs do OMS. Consulte [controlar alterações em seu ambiente com a solução controle de alterações](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitos do FIM

O recurso FIM da central de segurança do Azure pode estar Validando incorretamente a integridade dos seus arquivos Linux. Verifique se o FIM está operacional e configurado corretamente para o monitoramento de arquivos do Linux. Consulte [controlar alterações em seu ambiente com a solução controle de alterações](../change-tracking.md).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar o problema ou não conseguir resolver o problema, use um dos canais a seguir para obter mais suporte.

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
