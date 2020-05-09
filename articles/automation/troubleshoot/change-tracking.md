---
title: Solucionando problemas com o Controle de Alterações e o inventário
description: Saiba como solucionar e resolver problemas com a solução de inventário e Controle de Alterações de automação do Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582152"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Solucionar problemas de Controle de Alterações e inventário

Este artigo descreve como solucionar problemas de inventário e Controle de Alterações de automação do Azure.

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo Az no seu Hybrid Runbook Worker, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de automação, você pode atualizar seus módulos para a versão mais recente usando [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Cenário: os registros de Controle de Alterações e de inventário não são mostrados para computadores Windows

#### <a name="issue"></a>Problema

Você não verá nenhum resultado de Controle de Alterações e inventário para computadores Windows que estejam integrados.

#### <a name="cause"></a>Causa

Esse erro pode ter as seguintes causas:

* O agente de Log Analytics do Azure para Windows não está em execução.
* A comunicação de volta com a conta de automação está sendo bloqueada.
* Os pacotes de gerenciamento de Controle de Alterações e inventário não são baixados.
* A VM que está sendo integrada pode ter vindo de um computador clonado que não foi Sysprep com o agente Log Analytics para Windows instalado.

#### <a name="resolution"></a>Resolução

No computador do agente de Log Analytics, vá para **c:\Arquivos de Programas\microsoft Monitoring Agent\Agent\Tools** e execute os seguintes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se ainda precisar de ajuda, você poderá coletar informações de diagnóstico e contatar o suporte.

> [!NOTE]
> O agente de Log Analytics habilita o rastreamento de erros por padrão. Para habilitar mensagens de erro detalhadas como no exemplo anterior, use o `VER` parâmetro. Para rastreamentos de informações `INF` , use quando `StartTracing.cmd`invocar.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics agente para Windows não está em execução

Verifique se o agente do Log Analytics para Windows (**HealthService. exe**) está em execução no computador.

##### <a name="communication-to-automation-account-blocked"></a>Comunicação com a conta de automação bloqueada

Verifique Visualizador de Eventos no computador e procure todos os eventos que tenham a palavra `changetracking` em si.

Para saber mais sobre endereços e portas que devem ser permitidos para Controle de Alterações e inventário funcionar, confira [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Pacotes de gerenciamento não baixados

Verifique se os seguintes pacotes de gerenciamento de Controle de Alterações e inventário estão instalados localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM do computador clonado que não foi sysprepdo

Se estiver usando uma imagem clonada, execute o Sysprep na imagem primeiro e, em seguida, instale o agente de Log Analytics para Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Cenário: nenhum Controle de Alterações e resultados de inventário em computadores Linux

#### <a name="issue"></a>Problema

Você não vê nenhum resultado de inventário e Controle de Alterações para computadores Linux que são integrados para a solução. 

#### <a name="cause"></a>Causa
Aqui estão as possíveis causas específicas para esse problema:
* O agente do Log Analytics para Linux não está em execução.
* O agente do Log Analytics para Linux não está configurado corretamente.
* Há conflitos de FIM (monitoramento de integridade de arquivo).

#### <a name="resolution"></a>Resolução 

##### <a name="log-analytics-agent-for-linux-not-running"></a>O agente do Log Analytics para Linux não está em execução

Verifique se o daemon do agente de Log Analytics para Linux (**omsagent**) está em execução no seu computador. Execute a consulta a seguir no espaço de trabalho Log Analytics que está vinculado à sua conta de automação.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente. Provavelmente, há um problema de configuração local e você deve reinstalar o agente. Para obter informações sobre instalação e configuração, consulte [coletar dados de log com o agente de log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Se o computador aparecer nos resultados da consulta, verifique a configuração do escopo. Consulte [direcionando soluções de monitoramento no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Para obter mais soluções de problemas desse problema, consulte [problema: você não está vendo nenhum dado do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente do Log Analytics para Linux não configurado corretamente

O agente do Log Analytics para Linux pode não estar configurado corretamente para a coleção de saída de linha de comando e de log usando a ferramenta do coletor de logs do OMS. Consulte [controlar alterações em seu ambiente com a solução de controle de alterações e inventário](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitos do FIM

O recurso FIM da central de segurança do Azure pode estar Validando incorretamente a integridade dos seus arquivos Linux. Verifique se o FIM está operacional e configurado corretamente para o monitoramento de arquivos do Linux. Consulte [controlar alterações em seu ambiente com a solução de controle de alterações e inventário](../change-tracking.md).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não puder resolver o problema, tente um dos seguintes canais para obter suporte adicional:

* Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte- [@AzureSupport](https://twitter.com/azuresupport)se com o, a conta de Microsoft Azure oficial para melhorar a experiência do cliente. O suporte do Azure conecta a Comunidade do Azure a respostas, suporte e especialistas.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**.
