---
title: Obter eventos de integridade de recursos do Azure usando a API REST | Microsoft Docs
description: Use as APIs REST do Azure para obter os eventos de integridade para seus recursos do Azure.
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 353bd65b0466902e450e38677a350a177a1d602c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451387"
---
# <a name="get-resource-health-using-the-rest-api"></a>Obter Resource Health usando a API REST 

Este artigo de exemplo mostra como recuperar uma lista de eventos de integridade para os recursos do Azure na sua assinatura usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e os exemplos adicionais da API REST estão disponíveis na [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar a solicitação

Use esta solicitação HTTP `GET` para listar os eventos de integridade para sua assinatura para o intervalo de tempo entre `2018-05-16` e `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|Description|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|  
|*Autorização:*|Obrigatórios. Defina como um token de [acesso](/rest/api/azure/#authorization-code-grant-interactive-clients)de `Bearer` válido. |  

### <a name="uri-parameters"></a>Parâmetros de URI

| Nome | Description |
| :--- | :---------- |
| subscriptionId | A ID de assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento abrange a versão da API `2015-04-01`, incluída na URL acima.  |
| $filter | A opção de filtragem para reduzir o conjunto de resultados devolvidos. Os padrões permitidos para esse parâmetro estão disponíveis [na referência para a operação de Logs de atividades](/rest/api/monitor/activitylogs/list#uri-parameters). O exemplo mostrado captura todos os eventos em um intervalo de tempo entre 2018-05-16 e 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo da solicitação

Nenhum corpo de solicitação é necessário para esta operação.

## <a name="handle-the-response"></a>Tratar da resposta

O código de status 200 é devolvido com uma lista de valores de evento de integridade correspondente ao parâmetro de filtro, junto com um URI `nextlink` para recuperar a próxima página de resultados.

## <a name="example-response"></a>Exemplo de resposta 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
