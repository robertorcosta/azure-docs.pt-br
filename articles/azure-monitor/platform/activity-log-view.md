---
title: Exibir eventos do log de atividades do Azure no Azure Monitor
description: Exiba o log de atividades do Azure no Azure Monitor e recupere com o PowerShell, a CLI e a API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834604"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Exibir e recuperar eventos do log de atividades do Azure

O [log de atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para exibir e recuperar eventos do log de atividades.

## <a name="azure-portal"></a>Portal do Azure
Exiba o log de atividades de todos os recursos no menu **Monitor** no portal do Azure. Exiba o log de atividades de um recurso específico da opção **Log de Atividades** no menu desse recurso.

![Exibir log de atividades](./media/activity-logs-overview/view-activity-log.png)

É possível filtrar os eventos do log de atividades pelos seguintes campos:

* **Intervalo de tempo**: a hora de início e de término dos eventos.
* **Categoria**: a categoria do evento, conforme descrito em [Categorias no log de atividades](activity-log-view.md#categories-in-the-activity-log).
* **Assinatura**: um ou mais nomes de assinatura do Azure.
* **Grupo de recursos**: um ou mais grupos de recursos nas assinaturas selecionadas.
* **Recurso (nome)** : – o nome de um recurso específico.
* **Tipo de recurso**: o tipo de recurso, por exemplo, _Microsoft.Compute/virtualmachines_.
* **Nome da operação** – o nome de uma operação do Azure Resource Manager, por exemplo, _Microsoft.SQL/servers/Write_.
* **Gravidade**: O nível de severidade do evento. Os valores disponíveis são _Informativo_, _Aviso_, _Erro_, _Crítico_.
* **Evento iniciado por**: o usuário que realizou a operação.
* **Abrir a pesquisa**: caixa de pesquisa de texto aberto que procura essa cadeia de caracteres em todos os campos de todos os eventos.

## <a name="categories-in-the-activity-log"></a>Categorias no log de atividades
Cada evento no log de atividades tem uma categoria específica que são descritas na tabela a seguir. Para obter todos os detalhes sobre o esquema dessas categorias, veja o [esquema de eventos de Log de Atividades do Azure](activity-log-schema.md). 

| Categoria | Descrição |
|:---|:---|
| Administrativo | Contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Exemplos de eventos administrativos incluem _criar máquina virtual_ e _excluir grupo de segurança de rede_.<br><br>Cada ação tomada por um usuário ou aplicativo usando o Resource Manager é modelada como uma operação em um determinado tipo de recurso. Se o tipo de operação for _Gravação_, _Exclusão_ ou _Ação_, os registros do início e do êxito ou falha da operação são registrados na categoria Administrativa. Eventos administrativos também incluem alterações de controle de acesso baseado em função em uma assinatura. |
| Integridade do Serviço | Contém o registro de qualquer incidente de integridade do serviço ocorrido no Azure. Um exemplo de um evento de Integridade do Serviço _SQL Azure no Leste dos EUA está apresentando tempo de inatividade_. <br><br>Eventos de integridade do serviço são fornecidos em seis variedades: _Ação Necessária_, _Recuperação Assistida_, _Incidente_, _Manutenção_, _Informações_ ou _Segurança_. Esses eventos serão criados somente se você tiver um recurso na assinatura que seria impactado pelo evento.
| Integridade de recursos | Contém o registro de qualquer evento de integridade do recurso ocorrido nos recursos do Azure. Um exemplo de um evento de Integridade de Recursos é _Status de integridade da máquina virtual alterado para indisponível_.<br><br>Eventos de integridade de recursos podem representar um dos quatro status de integridade: _Disponível_, _Não disponível_, _Degradado_ e _Desconhecido_. Além disso, os eventos de integridade de recursos podem ser categorizados como _Iniciados pela plataforma_ ou _Iniciados pelo usuário_. |
| Alerta | Contém o registro de ativações para alertas do Azure. Um exemplo de evento de alerta é _A % de CPU em myVM está maior que 80 nos últimos cinco minutos_.|
| Autoscale | Contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em quaisquer configurações de dimensionamento automático que você definiu na sua assinatura. Um exemplo de um evento de dimensionamento automático é _Ação de dimensionamento automático vertical com falha_. |
| Recomendação | Contém eventos de recomendação do Assistente do Azure. |
| Segurança | Contém o registro de todos os alertas gerados pela Central de Segurança do Azure. Um exemplo de um evento de segurança é _Arquivo de extensão dupla suspeito executado_. |
| Política | Contém registros de todas as operações de ação de efeito executadas pelo Azure Policy. Exemplos de eventos de política incluem _Auditar_ e _Negar_. Cada ação tomada pelo Policy é modelada como uma operação em um recurso. |

## <a name="view-change-history"></a>Exibir histórico de alterações

Ao revisar o log de atividades, ele pode ajudar a ver quais alterações ocorreram durante esse evento. Você pode exibir essas informações com o **Histórico de alterações**. Selecione um evento do log de atividades que você deseja examinar melhor. Selecione a guia **Histórico de alterações (versão prévia)** para exibir as alterações associadas a esse evento.

![Lista de histórico de alterações para um evento](media/activity-logs-overview/change-history-event.png)

Se houver alterações associadas ao evento, você verá uma lista de alterações para selecionar. Isso abre a página **Histórico de alterações (versão prévia)** . Nessa página, você vê as alterações no recurso. No exemplo a seguir, podemos ver não apenas que a VM mudou de tamanho, mas qual era o tamanho anterior da VM antes da alteração e para que ela foi alterada.

![Página de histórico de alterações mostrando diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre o Histórico de alterações, confira [Obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Use o cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) para recuperar o log de atividades do PowerShell. A seguir, temos alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. Use o parâmetro **-MaxRecords** para consultar os últimos N eventos, além de 15 dias. Para eventos de acesso que ocorreram há mais 15 dias, use a API REST ou o SDK. Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.


Obter entradas de log criadas após uma data e hora específica:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obter entradas de log em um intervalo de data e hora:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico um intervalo de data e hora:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter as entradas de log com um autor de chamadas específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obter os últimos 1000 eventos:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
Use [az monitor activity-log](../samples/cli-samples.md#view-activity-log-for-a-subscription) para recuperar o log de atividades da CLI. A seguir, temos alguns exemplos comuns.


Veja todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obter entradas de log de um grupo de recursos específico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obter as entradas de log com um autor de chamadas específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obter logs por chamador em um tipo de recurso, dentro de um intervalo de datas:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Use a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) para recuperar o log de atividades de um cliente REST. A seguir, temos alguns exemplos comuns.

Obter logs de atividades com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obter logs de atividades com filtro e selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividades com selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividades sem filtro ou selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Próximas etapas

* [Leia uma visão geral dos logs de plataforma](platform-logs-overview.md)
* [Criar configuração de diagnóstico para enviar logs de atividades para outros destinos](diagnostic-settings.md)
