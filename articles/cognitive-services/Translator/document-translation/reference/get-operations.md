---
title: Operações de obtenção de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método Get Operations retorna uma lista de solicitações de lote enviadas e o status de cada solicitação.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612959"
---
# <a name="document-translation-get-operations"></a>Tradução de documentos: obter operações

O método Get Operations retorna uma lista de solicitações de lote enviadas e o status de cada solicitação. Essa lista contém apenas solicitações de lote enviadas pelo usuário (com base na assinatura). O status de cada solicitação é classificado por ID.

Se o número de solicitações exceder nosso limite de paginação, a paginação do servidor será usada. As respostas paginadas indicam um resultado parcial e incluem um token de continuação na resposta. A ausência de um token de continuação significa que nenhuma página adicional está disponível.

$top e $skip parâmetros de consulta podem ser usados para especificar um número de resultados a serem retornados e um deslocamento para a coleção.

O servidor honra os valores especificados pelo cliente. No entanto, os clientes devem estar preparados para lidar com as respostas que contêm um tamanho de página diferente ou conter um token de continuação.

Quando $top e $skip são incluídos, o servidor deve primeiro aplicar $skip e, em seguida, $top na coleção. 

> [!NOTE]
> Se o servidor não puder honrar $top e/ou $skip, o servidor deverá retornar um erro para o cliente informando sobre ele, em vez de apenas ignorar as opções de consulta. Isso reduz o risco de o cliente fazer suposições sobre os dados retornados.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `GET` para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|--- |--- |--- |
|$skip|Falso|Ignore as entradas de $skip na coleção. Quando $top e $skip são fornecidos, $skip é aplicado primeiro.|
|$top|Falso|Pegue as entradas de $top na coleção. Quando $top e $skip são fornecidos, $skip é aplicado primeiro.|

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|Descrição|
|--- |--- |
|200|OK. Solicitação bem-sucedida e retorna o status de todas as operações. HeadersRetry-After: integerETag: String|
|400|Solicitação inválida. Solicitação inválida. Verifique os parâmetros de entrada.|
|401|Não autorizado. Verifique suas credenciais.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|

## <a name="get-operations-response"></a>Resposta de operações Get

### <a name="successful-get-operations-response"></a>Resposta de operações Get com êxito

As informações a seguir são retornadas em uma resposta bem-sucedida.

|Nome|Type|Descrição|
|--- |--- |--- |
|id|string|Identificador da operação.|
|createdDateTimeUtc|string|Data e hora da criação da operação.|
|lastActionDateTimeUtc|string|Data e hora em que o status da operação foi atualizado.|
|status|String|Lista de status possíveis para o trabalho ou o documento: <ul><li>Canceled</li><li>Cancelando</li><li>Com falha</li><li>NotStarted</li><li>Executando</li><li>Com sucesso</li><li>ValidationFailed</li></ul>|
|resumo|StatusSummary[]|Resumo contendo os detalhes listados abaixo.|
|Resumo. total|inteiro|Contagem do total de documentos.|
|Resumo. falha|inteiro|Contagem de documentos com falha.|
|Resumo. êxito|inteiro|Contagem de documentos traduzidos com êxito.|
|Resumo. InProgress|inteiro|Contagem de documentos em andamento.|
|Summary. notYetStarted|inteiro|Contagem de documentos ainda não iniciados no processamento.|
|Resumo. cancelado|inteiro|Contagem de documentos cancelados.|
|Summary. totalCharacterCharged|inteiro|Contagem total de caracteres cobrados.|

###<a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
|code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
|destino|string|Obtém a origem do erro. Por exemplo, seria "Documents" ou "Document ID" no caso de um documento inválido.|
|innerError|InnerErrorV2|Novo formato de erro interno, que está em conformidade com as diretrizes de API de serviços cognitivas. Ele contém as propriedades obrigatórias ErrorCode, mensagem e propriedades opcionais destino, detalhes (par chave-valor), erro interno (isso pode ser aninhado).|
|innerError. Code|string|Obtém a cadeia de caracteres de erro do código.|
|innerError. Message|string|Obtém a mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem-sucedida

Veja a seguir um exemplo de uma resposta bem-sucedida.

```JSON
{
  "value": [
    {
      "id": "727bf148-f327-47a0-9481-abae6362f11e",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Succeeded",
      "summary": {
        "total": 10,
        "failed": 1,
        "success": 9,
        "inProgress": 0,
        "notYetStarted": 0,
        "cancelled": 0,
        "totalCharacterCharged": 0
      }
    }
  ]
}
```

### <a name="example-error-response"></a>Exemplo de resposta de erro

Veja a seguir um exemplo de uma resposta de erro. O esquema para outros códigos de erro é o mesmo.

Código de status: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Siga nosso início rápido para saber mais sobre como usar a tradução de documentos e a biblioteca de clientes.

> [!div class="nextstepaction"]
> [Introdução à tradução do documento](../get-started-with-document-translation.md)
