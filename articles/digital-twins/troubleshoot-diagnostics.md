---
title: Configurar o diagnóstico
titleSuffix: Azure Digital Twins
description: Consulte Como habilitar o registro em log com as configurações de diagnóstico.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: f4abf78c153bd3d61068e4b7607794d6ccf1ed04
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047668"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Solução de problemas do Azure digital gêmeos: log de diagnóstico

O Azure digital gêmeos coleta [métricas](troubleshoot-metrics.md) para sua instância de serviço que fornecem informações sobre o estado de seus recursos. Você pode usar essas métricas para avaliar a integridade geral do serviço de gêmeos digital do Azure e os recursos conectados a ele. Essas estatísticas voltadas para o usuário ajudam a ver o que está acontecendo com o gêmeos digital do Azure e ajudam a executar a análise da causa raiz sobre problemas sem a necessidade de contatar o suporte do Azure.

Este artigo mostra como ativar o log de **diagnóstico** para seus dados de métricas de sua instância de gêmeos digital do Azure. Você pode usar esses logs para ajudá-lo a solucionar problemas de serviço e definir configurações de diagnóstico para enviar métricas de gêmeos digitais do Azure para destinos diferentes. Você pode ler mais sobre essas configurações em [*criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Ativar as configurações de diagnóstico com o portal do Azure

Aqui está como habilitar as configurações de diagnóstico para sua instância do gêmeos digital do Azure:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. Selecione **configurações de diagnóstico** no menu e, em seguida, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

3. Na página a seguir, preencha os seguintes valores:
     * **Nome da configuração de diagnóstico**: dê um nome às configurações de diagnóstico.
     * **Detalhes da categoria**: escolha as operações que você deseja monitorar e marque as caixas para habilitar o diagnóstico para essas operações. As operações que podem ser informadas em configurações de diagnóstico são:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Para obter mais detalhes sobre essas opções, consulte a seção [*detalhes da categoria*](#category-details) abaixo.
     * **Detalhes de destino**: escolha onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções abaixo:
        - Enviar para o Log Analytics
        - Arquivar em uma conta de armazenamento
        - Transmitir para um hub de eventos

        Você pode ser solicitado a preencher detalhes adicionais se eles forem necessários para a seleção de destino.  
    
4. Salve as novas configurações. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecem no destino configurado de volta na página **configurações de diagnóstico** da instância do. 

## <a name="category-details"></a>Detalhes da categoria

Aqui estão mais detalhes sobre as categorias de log que podem ser selecionadas em **detalhes da categoria** ao definir as configurações de diagnóstico.

| Categoria do log | Description |
| --- | --- |
| ADTModelsOperation | Registrar em log todas as chamadas de API pertencentes a modelos |
| ADTQueryOperation | Registrar em log todas as chamadas de API pertencentes a consultas |
| ADTEventRoutesOperation | Registrar em log todas as chamadas de API pertencentes a rotas de eventos, bem como saída de eventos do Azure digital gêmeos para um serviço de ponto de extremidade como grade de eventos, hubs de eventos e barramento de serviço |
| ADTDigitalTwinsOperation | Registrar em log todas as chamadas de API pertencentes ao Azure digital gêmeos |

Cada categoria de log consiste em operações de gravação, leitura, exclusão e ação.  Eles são mapeados para chamadas à API REST da seguinte maneira:

| Tipo de evento | Operações da API REST |
| --- | --- |
| Gravar | PUT e PATCH |
| Ler | GET |
| Excluir | Delete (excluir) |
| Ação | POST |

Aqui está uma lista abrangente das operações e das [chamadas da API REST do Azure digital gêmeos](/rest/api/azure-digitaltwins/) que são registradas em log em cada categoria. 

>[!NOTE]
> Cada categoria de log contém várias operações/chamadas à API REST. Na tabela a seguir, cada categoria de log é mapeada para todas as operações/chamadas à API REST abaixo dela até que a próxima categoria de log seja listada. 

| Categoria do log | Operação | Chamadas à API REST e outros eventos |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/Models/Write | API de atualização de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/Models/Read | Modelos de entrelaçamento digital obter por ID e APIs de lista |
|  | Microsoft. DigitalTwins/Models/Delete | API de exclusão de modelos de entrelaçamento digital |
|  | Microsoft. DigitalTwins/modelos/ação | Modelos de entrelaçamento digital adicionar API |
| ADTQueryOperation | Microsoft. DigitalTwins/consulta/ação | API do gêmeos de consulta |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Adicionar API às rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Read | Rotas de eventos Get por ID e listar APIs |
|  | Microsoft. DigitalTwins/eventroutes/Delete | API de exclusão de rotas de eventos |
|  | Microsoft. DigitalTwins/eventroutes/Action | Falha ao tentar publicar eventos em um serviço de ponto de extremidade (não uma chamada à API) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Gêmeos digital-adicionar, adicionar relação, atualizar, atualizar componente |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digital gêmeos Get por ID, obter componente, obter relação por ID, listar relações de entrada, relações de lista |
|  | Microsoft. DigitalTwins/DigitalTwins/Delete | Gêmeos digital excluir, excluir relação |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Telemetria do componente de envio digital gêmeos, enviar telemetria |

## <a name="log-schemas"></a>Esquemas de log 

Cada categoria de log tem um esquema que define como os eventos nessa categoria são relatados. Cada entrada de log individual é armazenada como texto e formatada como um blob JSON. Os campos no log e os corpos JSON de exemplo são fornecidos para cada tipo de log abaixo. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` e `ADTQueryOperation` usam um esquema de log de API consistente; `ADTEventRoutesOperation` tem seu próprio esquema separado.

### <a name="api-log-schemas"></a>Esquemas de log de API

Esse esquema de log é consistente para `ADTDigitalTwinsOperation` , `ADTModelsOperation` e `ADTQueryOperation` . Ele contém informações pertinentes a chamadas de API para uma instância do gêmeos digital do Azure.

Aqui estão as descrições de campo e propriedade para logs de API.

| Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e a hora em que esse evento ocorreu, em UTC |
| `ResourceID` | Cadeia de caracteres | A ID de recurso Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | Cadeia de caracteres  | O tipo de ação que está sendo executada durante o evento |
| `OperationVersion` | Cadeia de caracteres | A versão da API utilizada durante o evento |
| `Category` | Cadeia de caracteres | O tipo de recurso que está sendo emitido |
| `ResultType` | Cadeia de caracteres | Resultado do evento |
| `ResultSignature` | Cadeia de caracteres | Código de status HTTP para o evento |
| `ResultDescription` | Cadeia de caracteres | Detalhes adicionais sobre o evento |
| `DurationMs` | Cadeia de caracteres | Quanto tempo demorou para executar o evento em milissegundos |
| `CallerIpAddress` | Cadeia de caracteres | Um endereço IP de origem mascarado para o evento |
| `CorrelationId` | Guid | Identificador exclusivo fornecido pelo cliente para o evento |
| `Level` | Cadeia de caracteres | A severidade de log do evento |
| `Location` | Cadeia de caracteres | A região onde o evento ocorreu |
| `RequestUri` | Uri | O ponto de extremidade utilizado durante o evento |

Veja a seguir os corpos de JSON de exemplo para esses tipos de logs.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>Esquemas de log de egresso

Este é o esquema para `ADTEventRoutesOperation` logs. Eles contêm detalhes referentes a exceções e as operações de API em relação aos pontos de extremidade de saída conectados a uma instância de gêmeos digital do Azure.

|Nome do campo | Tipo de dados | Descrição |
|-----|------|-------------|
| `Time` | Datetime | A data e a hora em que esse evento ocorreu, em UTC |
| `ResourceId` | Cadeia de caracteres | A ID de recurso Azure Resource Manager para o recurso em que o evento ocorreu |
| `OperationName` | Cadeia de caracteres  | O tipo de ação que está sendo executada durante o evento |
| `Category` | Cadeia de caracteres | O tipo de recurso que está sendo emitido |
| `ResultDescription` | Cadeia de caracteres | Detalhes adicionais sobre o evento |
| `Level` | Cadeia de caracteres | A severidade de log do evento |
| `Location` | Cadeia de caracteres | A região onde o evento ocorreu |
| `EndpointName` | Cadeia de caracteres | O nome do ponto de extremidade de egresso criado no gêmeos digital do Azure |

Veja a seguir os corpos de JSON de exemplo para esses tipos de logs.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como configurar o diagnóstico, consulte [*coletar e consumir dados de log dos recursos do Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Para obter informações sobre as métricas do gêmeos digital do Azure, consulte [*solução de problemas: Exibir métricas com Azure monitor*](troubleshoot-metrics.md).
* Para ver como habilitar alertas para suas métricas, consulte [*solução de problemas: configurar alertas*](troubleshoot-alerts.md).