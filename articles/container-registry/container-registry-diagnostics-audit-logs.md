---
title: Coletar & analisar registros de recursos
description: Registre e analise eventos de registro de recursos para o Registro de Contêineres do Azure, como autenticação, empurrão de imagem e puxão de imagem.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409636"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Registros do Registro de Contêineres do Azure para avaliação e auditoria de diagnóstico

Este artigo explica como coletar dados de log para um registro de contêiner do Azure usando recursos do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor coleta [registros de recursos](../azure-monitor/platform/platform-logs-overview.md) (anteriormente chamados de *logs de diagnóstico)* para eventos orientados pelo usuário em seu registro. Coletar e consumir esses dados para atender a necessidades como:

* Eventos de autenticação de registro de auditoria para garantir segurança e conformidade 

* Forneça uma trilha completa de atividade em artefatos de registro, como eventos de puxar e puxar para que você possa diagnosticar problemas operacionais com o seu registro 

A coleta de dados de registro de recursos usando o Azure Monitor pode incorrer em custos adicionais. Consulte [os preços do Monitor Do Azure](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventos de repositório

Os seguintes eventos de nível de repositório para imagens e outros artefatos estão atualmente registrados:

* **Eventos de push**
* **Eventos de atração**
* **Eventos untag**
* **Excluir eventos** (incluindo eventos de exclusão de repositórios)

Eventos de nível repositório que não estão registrados no momento: expurgar eventos.

## <a name="registry-resource-logs"></a>Registros de recursos de registro

Os registros de recursos contêm informações emitidas pelos recursos do Azure que descrevem sua operação interna. Para um registro de contêiner do Azure, os registros contêm eventos de autenticação e nível de repositório armazenados nas tabelas a seguir. 

* **ContêinerRegistroEventos** de login - Eventos de autenticação de registro e status, incluindo a identidade de entrada e endereço IP
* **ContêinerRegistroRepositoryEventos** - Operações como empurrar e puxar para imagens e outros artefatos em repositórios de registro
* **Métricas de registro de contêineres do AzureMetrics,** - [Container registry metrics](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) como contagem agregada de push e pull.

Para operações, os dados de log incluem:
  * Status de sucesso ou falha
  * Selos de horário de início e fim

Além dos registros de recursos, o Azure fornece um registro de [atividades,](../azure-monitor/platform/platform-logs-overview.md)um único registro em nível de assinatura de eventos de gerenciamento do Azure, como a criação ou exclusão de um registro de contêiner.

## <a name="enable-collection-of-resource-logs"></a>Habilitar a coleta de registros de recursos

A coleta de registros de recursos para um registro de contêiner não é habilitada por padrão. Habilite explicitamente as configurações de diagnóstico para cada registro que você deseja monitorar. Para obter opções para ativar as configurações de diagnóstico, consulte [Criar configuração de diagnóstico para coletar registros e métricas da plataforma no Azure](../azure-monitor/platform/diagnostic-settings.md).

Por exemplo, para exibir logs e métricas de um registro de contêiner em quase tempo real no Azure Monitor, colete os registros de recursos em um espaço de trabalho do Log Analytics. Para habilitar essa configuração de diagnóstico usando o portal Azure:

1. Se você ainda não tiver um espaço de trabalho, crie um espaço de trabalho usando o [portal Azure](../azure-monitor/learn/quick-create-workspace.md). Para minimizar a latência na coleta de dados, certifique-se de que o espaço de trabalho esteja na **mesma região** que o registro do contêiner.
1. No portal, selecione o registro e selecione **As configurações de Monitoramento > Diagnóstico > adicionar configuração diagnóstica**.
1. Digite um nome para a configuração e **selecione Enviar para Log Analytics**.
1. Selecione o espaço de trabalho para os registros de diagnóstico do registro.
1. Selecione os dados de log que deseja coletar e clique **em Salvar**.

A imagem a seguir mostra a criação de uma configuração de diagnóstico para um registro usando o portal.

![Habilitar configurações de diagnóstico](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Colete apenas os dados que você precisa, equilibrando o custo e suas necessidades de monitoramento. Por exemplo, se você só precisar auditar eventos de autenticação, selecione apenas o registro **ContainerRegistryEvents.** 

## <a name="view-data-in-azure-monitor"></a>Ver dados no Azure Monitor

Depois de habilitar a coleta de registros de diagnóstico no Log Analytics, pode levar alguns minutos para que os dados apareçam no Azure Monitor. Para visualizar os dados no portal, selecione o registro e selecione **Monitorando > Logs**. Selecione uma das tabelas que contêm dados para o registro. 

Executar consultas para visualizar os dados. Várias consultas de amostra são fornecidas ou executam as suas próprias. Por exemplo, a consulta a seguir recupera as 24 horas mais recentes de dados da tabela **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

A imagem a seguir mostra a saída da amostra:

![Consultar dados de log](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Para obter um tutorial sobre como usar o Log Analytics no portal Azure, consulte Comece com o [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)ou tente o [ambiente de demonstração do](https://portal.loganalytics.io/demo)Log Analytics . 

Para obter mais informações sobre consultas de log, consulte [Visão geral das consultas de log no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="additional-query-examples"></a>Exemplos adicionais de consulta

#### <a name="100-most-recent-registry-events"></a>100 eventos de registro mais recentes

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Destinos adicionais de log

Além de enviar os logs para o Log Analytics, ou como uma alternativa, um cenário comum é selecionar uma conta do Azure Storage como destino de log. Para arquivar logins no Azure Storage, crie uma conta de armazenamento antes de ativar o arquivamento através das configurações de diagnóstico.

Você também pode transmitir eventos de registro de diagnóstico para [um Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Os Hubs de Eventos podem incluir milhões de eventos por segundo, os quais você pode transformar e armazenar usando qualquer provedor de análise em tempo real. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como usar [o Log Analytics](../azure-monitor/log-query/get-started-portal.md) e criar [consultas de log](../azure-monitor/log-query/get-started-queries.md).
* Consulte [visão geral dos logs da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md) para saber mais sobre os logs de plataforma que estão disponíveis em diferentes camadas do Azure.

