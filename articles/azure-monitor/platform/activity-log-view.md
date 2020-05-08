---
title: Exibir eventos do log de atividades do Azure no Azure Monitor
description: Exiba o log de atividades do Azure em Azure Monitor e recupere com o PowerShell, a CLI e a API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 4ea29888d4dcf589e3e5d4dfe594f5f4bff2287e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559983"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Exibir e recuperar eventos do log de atividades do Azure

O [log de atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para exibir e recuperar eventos do log de atividades.

## <a name="azure-portal"></a>Portal do Azure
Exiba o log de atividades de todos os recursos no menu **monitorar** na portal do Azure. Exiba o log de atividades de um recurso específico da opção **log de atividades** no menu desse recurso.

![Exibir log de atividades](./media/activity-logs-overview/view-activity-log.png)

Você pode filtrar eventos do log de atividades pelos seguintes campos:

* **TimeSpan**: a hora de início e de término dos eventos.
* **Categoria**: a categoria de evento, conforme descrito em [categorias no log de atividades](activity-log-view.md#categories-in-the-activity-log).
* **Assinatura**: um ou mais nomes de assinatura do Azure.
* **Grupo de recursos**: um ou mais grupos de recursos dentro das assinaturas selecionadas.
* **Recurso (nome)**:-o nome de um recurso específico.
* **Tipo de recurso**: o tipo de recurso, por exemplo, _Microsoft. Compute/VirtualMachines_.
* **Nome da operação** -o nome de uma operação de Azure Resource Manager, por exemplo, _Microsoft. SQL/Servers/Write_.
* **Severidade**: o nível de severidade do evento. Os valores disponíveis são _informativo_, _aviso_, _erro_, _crítico_.
* **Evento iniciado por**: o usuário que realizou a operação.
* **Abrir pesquisa**: caixa de pesquisa de texto aberta que pesquisa essa cadeia de caracteres em todos os campos em todos os eventos.

## <a name="categories-in-the-activity-log"></a>Categorias no log de atividades
Cada evento no log de atividades tem uma categoria específica que são descritas na tabela a seguir. Para obter todos os detalhes sobre o esquema dessas categorias, veja o [esquema de eventos de Log de Atividades do Azure](activity-log-schema.md). 

| Categoria | Descrição |
|:---|:---|
| Administrativo | Contém o registro de todas as operações de criação, atualização, exclusão e ação executadas por meio do Resource Manager. Exemplos de eventos administrativos incluem _criar máquina virtual_ e _excluir grupo de segurança de rede_.<br><br>Cada ação tomada por um usuário ou aplicativo usando o Resource Manager é modelada como uma operação em um determinado tipo de recurso. Se o tipo de operação for _gravação_, _exclusão_ou _ação_, os registros de início e êxito ou falha da operação serão registrados na categoria administrativa. Os eventos administrativos também incluem quaisquer alterações no controle de acesso baseado em função em uma assinatura. |
| Integridade do Serviço | Contém o registro de qualquer incidente de integridade do serviço que ocorreu no Azure. Um exemplo de SQL Azure de eventos de integridade do serviço _no leste dos EUA está apresentando tempo de inatividade_. <br><br>Os eventos de integridade do serviço são fornecidos em seis variedades: _ação necessária_, _recuperação assistida_, _incidente_, _manutenção_, _informações_ou _segurança_. Esses eventos serão criados somente se você tiver um recurso na assinatura que seria impactado pelo evento.
| Integridade de recursos | Contém o registro dos eventos de integridade do recurso que ocorreram para os recursos do Azure. Um exemplo de um evento de Resource Health é o _status de integridade da máquina virtual alterado para indisponível_.<br><br>Resource Health eventos podem representar um dos quatro status de integridade: _disponível_, _indisponível_, _degradado_e _desconhecido_. Além disso, Resource Health eventos podem ser categorizados como sendo _iniciado pela plataforma_ ou _pelo usuário_. |
| Alerta | Contém o registro de ativações para alertas do Azure. Um exemplo de um evento de alerta é _% de CPU em myVM tem mais de 80 para os últimos 5 minutos_.|
| Autoscale | Contém o registro de todos os eventos relacionados à operação do mecanismo de dimensionamento automático com base em qualquer configuração de dimensionamento automático que você definiu em sua assinatura. Um exemplo de um evento de dimensionamento automático é a _ação de escalabilidade vertical com falha_. |
| Recomendação | Contém eventos de recomendação do Azure Advisor. |
| Segurança | Contém o registro de todos os alertas gerados pela central de segurança do Azure. Um exemplo de um evento de segurança é um _arquivo de extensão dupla suspeito executado_. |
| Política | Contém registros de todas as operações de ação de efeito executadas por Azure Policy. Exemplos de eventos de política incluem _auditoria_ e _negação_. Cada ação tomada pelo Policy é modelada como uma operação em um recurso. |

## <a name="view-change-history"></a>Exibir histórico de alterações

Ao examinar o log de atividades, ele pode ajudar a ver quais alterações ocorreram durante esse evento. Você pode exibir essas informações com o **histórico de alterações**. Selecione um evento do log de atividades que você deseja que pareça mais profundo. Selecione a guia **histórico de alterações (versão prévia)** para exibir as alterações associadas a esse evento.

![Lista de histórico de alterações para um evento](media/activity-logs-overview/change-history-event.png)

Se houver alterações associadas ao evento, você verá uma lista de alterações que pode selecionar. Isso abre a página **histórico de alterações (versão prévia)** . Nessa página, você vê as alterações no recurso. Como você pode ver no exemplo a seguir, podemos ver não apenas que a VM mudou de tamanhos, mas qual era o tamanho anterior da VM antes da alteração e para que ela foi alterada.

![Página de histórico de alterações mostrando diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre o histórico de alterações, consulte [obter alterações de recurso](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Use o cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) para recuperar o log de atividades do PowerShell. A seguir estão alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. Use o parâmetro **-MaxRecord** para consultar os últimos N eventos além de 15 dias. Para acessar eventos com mais de 15 dias, use a API REST ou o SDK. Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.


Obter entradas de log criadas após uma data e hora específica:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obter entradas de log entre um intervalo de data/hora:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico entre um intervalo de data/hora:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obter os últimos 1000 eventos:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
Use [AZ monitor Activity-log](cli-samples.md#view-activity-log-for-a-subscription) para recuperar o log de atividades da CLI. A seguir estão alguns exemplos comuns.


Exiba todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obter entradas de log de um grupo de recursos específico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obter entradas de log com um chamador específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obter logs pelo chamador em um tipo de recurso, dentro de um intervalo de datas:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Use a [API rest Azure monitor](https://docs.microsoft.com/rest/api/monitor/) para recuperar o log de atividades de um cliente REST. A seguir estão alguns exemplos comuns.

Obter logs de atividade com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obter logs de atividade com filtro e selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividade com SELECT:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obter logs de atividade sem filtro ou selecionar:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Próximas etapas

* [Leia uma visão geral dos logs da plataforma](platform-logs-overview.md)
* [Criar configuração de diagnóstico para enviar logs de atividade para outros destinos](diagnostic-settings.md)
