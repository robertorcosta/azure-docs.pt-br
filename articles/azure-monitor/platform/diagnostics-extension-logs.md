---
title: Use o armazenamento blob para IIS e armazenamento de mesa para eventos no Monitor Azure | Microsoft Docs
description: O Azure Monitor pode ler os registros dos serviços do Azure que escrevem diagnósticos em registros de armazenamento de tabelaou IIS gravados para armazenamento blob.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672388"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Coletar dados da extensão de diagnóstico sinuoso do Azure para o Azure Monitor Logs
A extensão de diagnóstico do Azure é um [agente no Azure Monitor](agents-overview.md) que coleta dados de monitoramento do sistema operacional convidado de recursos computacionais do Azure, incluindo máquinas virtuais. Este artigo descreve como coletar dados coletados pela extensão de diagnóstico do Azure Storage para o Azure Monitor Logs.

> [!NOTE]
> O agente Log Analytics no Azure Monitor é tipicamente o método preferido para coletar dados do sistema operacional convidado no Azure Monitor Logs. Consulte [a visão geral dos agentes do Monitor Do Azure](agents-overview.md) para obter uma comparação detalhada dos agentes.

## <a name="supported-data-types"></a>Tipos de dados com suporte
A extensão de diagnóstico do Azure armazena dados em uma conta do Azure Storage. Para que o Azure Monitor Logs colete esses dados, ele deve estar nos seguintes locais:

| Tipo de Log | Tipo de recurso | Location |
| --- | --- | --- |
| Logs IIS |Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |wad-iis-logfiles (Armazenamento de Blobs) |
| syslog |Máquinas Virtuais |LinuxsyslogVer2v0 (Armazenamento de Tabelas) |
| Eventos operacionais do Service Fabric |Nós do Service Fabric |WADServiceFabricSystemEventTable |
| Eventos dos Reliable Actors do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableActorEventTable |
| Arquitetura de Serviços Confiáveis do Service Fabric |Nós do Service Fabric |WADServiceFabricReliableServiceEventTable |
| Logs de Eventos do Windows |Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |WADWindowsEventLogsTable (Armazenamento de Tabelas) |
| Logs do ETW do Windows |Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho |WADETWEventTable (Armazenamento de Tabelas) |

## <a name="data-types-not-supported"></a>Tipos de dados não suportados

- Dados de desempenho do sistema operacional convidado
- Logs iIS de sites do Azure


## <a name="enable-azure-diagnostics-extension"></a>Habilite a extensão de diagnósticos do Azure
Consulte Instalar e configurar a [extensão de diagnóstico do Windows Azure (WAD)](diagnostics-extension-windows-install.md) ou [usar o Linux Diagnostic Extension para monitorar métricas e registros](../../virtual-machines/extensions/diagnostics-linux.md) para obter detalhes sobre a instalação e configuração da extensão de diagnóstico. Isso permitirá que você especifique a conta de armazenamento e configure a coleta dos dados que deseja encaminhar aos Logs do Monitor do Azure.


## <a name="collect-logs-from-azure-storage"></a>Coletar logs do Armazenamento Azure
Use o seguinte procedimento para permitir a coleta de dados de extensão de diagnósticos de uma conta do Azure Storage:

1. No portal Azure, vá para **Log Analytics Workspaces** e selecione seu espaço de trabalho.
1. Clique **em Armazenar contas logs** na seção Fontes de dados do espaço de **trabalho** do menu.
2. Clique **em Adicionar**.
3. Selecione a **conta Armazenamento** que contém os dados a serem coletados.
4. Selecione o **Tipo de dados** que deseja coletar.
5. O valor para Origem é preenchido automaticamente com base no tipo de dados.
6. Clique em **OK** para salvar a configuração.
7. Repita para tipos adicionais de dados.

Em aproximadamente 30 minutos, você pode ver dados da conta de armazenamento no espaço de trabalho do Log Analytics. Você só verá os dados gravados no armazenamento após a configuração ser aplicada. O espaço de trabalho não lê os dados pré-existentes da conta de armazenamento.

> [!NOTE]
> O portal não valida se a fonte existe na conta de armazenamento ou se novos dados estão sendo gravados.



## <a name="next-steps"></a>Próximas etapas

* [Coletar logs e métricas para serviços do Azure](collect-azure-metrics-logs.md) para serviços do Azure com suporte.
* [Habilitar Soluções](../../azure-monitor/insights/solutions.md) para fornecer informações sobre os dados.
* [Usar consultas de pesquisa](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados.
