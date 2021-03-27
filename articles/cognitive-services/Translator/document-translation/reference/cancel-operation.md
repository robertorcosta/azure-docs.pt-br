---
title: Método de operação de cancelamento de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método Cancel Operations cancela uma operação em fila ou processamento no momento.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612982"
---
# <a name="document-translation-cancel-operations"></a>Tradução de documento: cancelar operações

Cancele uma operação de processamento ou em fila no momento. Uma operação não será cancelada se já estiver concluída ou com falha ou cancelamento. Uma solicitação inadequada será retornada. Todos os documentos que concluíram a tradução não serão cancelados e serão cobrados. Todos os documentos pendentes serão cancelados, se possível.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `DELETE` para:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|-----|-----|-----|
|id|True|A ID da operação.|

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|-----|-----|
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

| Código de status| Descrição|
|-----|-----|
|200|OK. A solicitação de cancelamento foi enviada|
|401|Não autorizado. Verifique suas credenciais.|
|404|Não encontrado. O recurso não foi encontrado. 
|500|Erro Interno do Servidor.
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|

## <a name="cancel-operations-response"></a>Cancelar resposta de operações

### <a name="successful-response"></a>Resposta bem-sucedida

As informações a seguir são retornadas em uma resposta bem-sucedida.

|Nome|Type|Descrição|
|--- |--- |--- |
|id|string|Identificador da operação.|
|createdDateTimeUtc|string|Data e hora da criação da operação.|
|lastActionDateTimeUtc|string|Data e hora em que o status da operação foi atualizado.|
|status|String|Lista de status possíveis para o trabalho ou o documento: <ul><li>Canceled</li><li>Cancelando</li><li>Com falha</li><li>NotStarted</li><li>Executando</li><li>Com sucesso</li><li>ValidationFailed</li></ul>|
|resumo|StatusSummary|Resumo contendo os detalhes listados abaixo.|
|Resumo. total|inteiro|Contagem do total de documentos.|
|Resumo. falha|inteiro|Contagem de documentos com falha.|
|Resumo. êxito|inteiro|Contagem de documentos traduzidos com êxito.|
|Resumo. InProgress|inteiro|Contagem de documentos em andamento.|
|Summary. notYetStarted|inteiro|Contagem de documentos ainda não iniciados no processamento.|
|Resumo. cancelado|inteiro|Número de cancelado.|
|Summary. totalCharacterCharged|inteiro|Total de caracteres cobrados pela API.|

### <a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
|code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
|destino|string|Obtém a origem do erro. Por exemplo, seria "Documents" ou "Document ID" para um documento inválido.|
|innerError|InnerErrorV2|Novo formato de erro interno, que está em conformidade com as diretrizes de API de serviços cognitivas. Ele contém as propriedades obrigatórias ErrorCode, Message e propriedades opcionais Target, detalhes (par chave-valor), erro interno (pode ser aninhado).|
|innerError. Code|string|Obtém a cadeia de caracteres de erro do código.|
|Inner. Erro. Message|string|Obtém a mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem-sucedida

O objeto JSON a seguir é um exemplo de uma resposta bem-sucedida.

Código de status: 200

```JSON
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
```

### <a name="example-error-response"></a>Exemplo de resposta de erro

O objeto JSON a seguir é um exemplo de uma resposta de erro. O esquema para outros códigos de erro é o mesmo.

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
