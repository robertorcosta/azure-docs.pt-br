---
title: Status de documentos da operação de obtenção de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método de status obter documentos de operação retorna o status de todos os documentos em uma solicitação de tradução de documento em lote.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612961"
---
# <a name="document-translation-get-operation-documents-status"></a>Tradução de documento: obter status de documentos de operação

O método de status obter documentos de operação retorna o status de todos os documentos em uma solicitação de tradução de documento em lote.

Os documentos incluídos na resposta são classificados por ID de documento em ordem decrescente. Se o número de documentos na resposta exceder nosso limite de paginação, a paginação do servidor será usada. As respostas paginadas indicam um resultado parcial e incluem um token de continuação na resposta. A ausência de um token de continuação significa que nenhuma página adicional está disponível.

$top e $skip parâmetros de consulta podem ser usados para especificar um número de resultados a serem retornados e um deslocamento para a coleção. O servidor honra os valores especificados pelo cliente. No entanto, os clientes devem estar preparados para lidar com as respostas que contêm um tamanho de página diferente ou conter um token de continuação.

Quando $top e $skip são incluídos, o servidor deve primeiro aplicar $skip e, em seguida, $top na coleção.

> [!NOTE]
> Se o servidor não puder honrar $top e/ou $skip, o servidor deverá retornar um erro para o cliente informando sobre ele, em vez de apenas ignorar as opções de consulta. Isso reduz o risco de o cliente fazer suposições sobre os dados retornados.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `GET` para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|id|True|A ID da operação.|
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
|200|OK. Solicitação bem-sucedida e retorna o status dos documentos. HeadersRetry-After: integerETag: String|
|400|Solicitação inválida. Verifique os parâmetros de entrada.|
|401|Não autorizado. Verifique suas credenciais.|
|404|O recurso não foi encontrado.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|


## <a name="get-operation-documents-status-response"></a>Obter resposta de status de documentos de operação

### <a name="successful-get-operation-documents-status-response"></a>Resposta de status de obter documentos de operação bem-sucedida

As informações a seguir são retornadas em uma resposta bem-sucedida.

|Nome|Type|Descrição|
|--- |--- |--- |
|@nextLink|string|URL da próxima página. NULL se não houver mais nenhuma página disponível.|
|value|DocumentStatusDetail []|O status de detalhes de documentos individuais listados abaixo.|
|valor. caminho|string|Local do documento ou da pasta.|
|valor. createdDateTimeUtc|string|Data e hora da criação da operação.|
|valor. lastActionDateTimeUt|string|Data e hora em que o status da operação foi atualizado.|
|valor. status|status|Lista de status possíveis para trabalho ou documento.<ul><li>Canceled</li><li>Cancelando</li><li>Com falha</li><li>NotStarted</li><li>Executando</li><li>Com sucesso</li><li>ValidationFailed</li></ul>|
|value.to|string|Para o idioma.|
|valor. Progress|string|Progresso da tradução, se disponível.|
|value.id|string|ID do documento.|
|valor. characterCharged|inteiro|Caracteres cobrados pela API.|

### <a name="error-response"></a>Resposta de erro

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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
