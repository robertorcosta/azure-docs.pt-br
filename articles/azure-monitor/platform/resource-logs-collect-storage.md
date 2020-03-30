---
title: Arquivar registros de recursos do Azure para a conta de armazenamento | Microsoft Docs
description: Saiba como arquivar seus registros de recursos do Azure para retenção a longo prazo em uma conta de armazenamento.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274210"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Arquivar logs de recursos do Azure na conta de armazenamento
[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo logs de atividades do Azure e registros de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem.  Este artigo descreve a coleta de logs de plataforma em uma conta de armazenamento do Azure para reter dados para arquivamento.

## <a name="prerequisites"></a>Pré-requisitos
Você precisa [criar uma conta de armazenamento Azure](../../storage/common/storage-account-create.md) se você ainda não tiver uma. A conta de armazenamento não precisa estar na mesma assinatura que os logs de envio de recursos, desde que o usuário que configura a configuração tenha acesso RBAC apropriado a ambas as assinaturas.


> [!IMPORTANT]
> As contas Azure Data Lake Storage Gen2 não são suportadas no momento como destino para configurações de diagnóstico, embora possam ser listadas como uma opção válida no portal Azure.


Você não deve usar uma conta de armazenamento existente que tenha outros dados não-monitorados armazenados nele para que você possa controlar melhor o acesso aos dados. Se você estiver arquivando os registros de atividades e recursos juntos, porém, você pode optar por usar a mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Envie logs de plataforma para armazenamento e outros destinos, criando uma configuração de diagnóstico para um recurso do Azure. Consulte [Criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.


## <a name="collect-data-from-compute-resources"></a>Coletar dados de recursos computacionais
As configurações de diagnóstico coletarão registros de recursos para os recursos de computação do Azure como qualquer outro recurso, mas não seu sistema operacional convidado ou cargas de trabalho. Para coletar esses dados, instale o [agente Windows Azure Diagnostics](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Esquema de logs de plataforma na conta de armazenamento

Uma vez criada a configuração de diagnóstico, um recipiente de armazenamento é criado na conta de armazenamento assim que um evento ocorre em uma das categorias de log habilitadas. As bolhas dentro do recipiente usam a seguinte convenção de nomeação:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, a bolha de um grupo de segurança de rede pode ter um nome semelhante ao seguinte:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor minucioso (m=00) é sempre 00, uma vez que os eventos de registro de recursos são divididos em bolhas individuais por hora.

Dentro do arquivo PT1H.json, cada evento é armazenado com o seguinte formato. Isso usará um esquema de nível superior comum, mas será único para cada serviço do Azure, conforme descrito no [esquema de logs de recursos](diagnostic-logs-schema.md) e esquema de registro de [atividades](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Os logs de plataforma são gravados para o armazenamento blob usando [linhas JSON](http://jsonlines.org/), onde cada evento é uma linha e o caractere newline indica um novo evento. Esse formato foi implementado em novembro de 2018. Antes desta data, os logs foram gravados para o armazenamento blob como um conjunto json de registros conforme descrito em Prepare para a alteração de [formato para logs da plataforma Azure Monitor arquivados em uma conta de armazenamento](resource-logs-blob-format.md).

## <a name="next-steps"></a>Próximas etapas

* [Leia mais sobre registros de recursos.](platform-logs-overview.md)
* [Crie configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md).
* [Baixe blobs para análise](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Arquive os registros do Azure Active Directory com o Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
