---
title: Método de formatos de Glossário de obtenção de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método obter formatos de Glossário retorna a lista de formatos de Glossário com suporte.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: fa4f89fb312e76d72447552b156d35bf3d0404bc
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612968"
---
# <a name="document-translation-get-glossary-formats"></a>Tradução de documentos: obter formatos de Glossário

O método obter formatos de Glossário retorna uma lista de formatos de Glossário com suporte no serviço de tradução de documentos. A lista inclui a extensão de arquivo comum usada.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `GET` para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/glossaries/formats
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|Descrição|
|--- |--- |
|200|OK. Retorna a lista de formatos de arquivo de Glossário com suporte.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|


## <a name="get-glossary-formats-response"></a>Obter resposta de formatos de Glossário

Tipo base para o retorno de lista na API obter formatos de glossário.

### <a name="successful-get-glossary-formats-response"></a>Resposta de obter formatos de Glossário bem-sucedida

Tipo base para o retorno de lista na API obter formatos de glossário.

|Código de status|Descrição|
|--- |--- |
|200|OK. Retorna a lista de formatos de arquivo de Glossário com suporte.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|Excesso de requestsServer temporária indisponível|

### <a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
|code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
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
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ],
            "versions": []
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
