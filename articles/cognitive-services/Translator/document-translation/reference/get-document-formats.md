---
title: Método de formatação de documentos de obtenção de tradução de documentos
titleSuffix: Azure Cognitive Services
description: O método obter formatos de documento retorna uma lista de formatos de documento com suporte.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 3cd74f70416d2049a0fb969371507c292dd47fdf
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612971"
---
# <a name="document-translation-get-document-formats"></a>Tradução de documentos: obter formatos de documento

O método obter formatos de documento retorna uma lista de formatos de documento com suporte pelo serviço de tradução de documentos. A lista inclui a extensão de arquivo comum e o tipo de conteúdo se estiver usando a API de carregamento.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `GET` para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/documents/formats
```

Saiba como localizar seu [nome de domínio personalizado](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Todas as solicitações de API para o serviço de tradução de documentos exigem um ponto de extremidade de domínio personalizado**.
> * Você não pode usar o ponto de extremidade encontrado em suas chaves de recurso portal do Azure e na página de _ponto de extremidade_ nem no ponto de extremidade do tradutor global — `api.cognitive.microsofttranslator.com` – para fazer solicitações HTTP para a tradução do documento.

## <a name="request-headers"></a>Cabeçalhos da solicitação

Os cabeçalhos de solicitação são:

|Cabeçalhos|Descrição|
|-----|-----|
|Ocp-Apim-Subscription-Key|Cabeçalho de solicitação necessário|

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|Descrição|
|-----|-----|
|200|OK. Retorna a lista de formatos de arquivo de documento com suporte.|
|500|Erro Interno do Servidor.|
|Outros códigos de status|<ul><li>Número excessivo de solicitações</li><li>Servidor temporário não disponível</li></ul>|

## <a name="file-format-response"></a>Resposta de formato de arquivo

### <a name="successful-fileformatlistresult-response"></a>Resposta fileFormatListResult bem-sucedida

As informações a seguir são retornadas em uma resposta bem-sucedida.

|Nome|Type|Descrição|
|--- |--- |--- |
|value|FileFormat []|FileFormat [] contém os detalhes listados abaixo.|
|valor. Format|string[]|Tipos de conteúdo com suporte para este formato.|
|extensões de valor.|string[]|Extensão de arquivo com suporte para este formato.|
|valor. contentTypes|string[]|Nome do formato.|
|valor. versões|String[]|Versão com suporte.|

### <a name="error-response"></a>Resposta de erro

|Nome|Type|Descrição|
|--- |--- |--- |
 |code|string|Enums que contêm códigos de erro de alto nível. Valores possíveis:<ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não Autorizado</li></ul>|
|message|string|Obtém a mensagem de erro de alto nível.|
|innerError|InnerErrorV2|Novo formato de erro interno, que está em conformidade com as diretrizes de API de serviços cognitivas. Ele contém as propriedades obrigatórias ErrorCode, mensagem e propriedades opcionais destino, detalhes (par chave-valor), erro interno (isso pode ser aninhado).|
|innerError. Code|string|Obtém a cadeia de caracteres de erro do código.|
|innerError. Message|string|Obtém a mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem-sucedida
Veja a seguir um exemplo de uma resposta bem-sucedida.

Código de status: 200

```JSON
{
  "value": [
    {
      "format": "PlainText",
      "fileExtensions": [
        ".txt"
      ],
      "contentTypes": [
        "text/plain"
      ],
      "versions": []
    },
    {
      "format": "PortableDocumentFormat",
      "fileExtensions": [
        ".pdf"
      ],
      "contentTypes": [
        "application/pdf"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlPresentation",
      "fileExtensions": [
        ".pptx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.presentationml.presentation"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlSpreadsheet",
      "fileExtensions": [
        ".xlsx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
      ],
      "versions": []
    },
    {
      "format": "OutlookMailMessage",
      "fileExtensions": [
        ".msg"
      ],
      "contentTypes": [
        "application/vnd.ms-outlook"
      ],
      "versions": []
    },
    {
      "format": "HtmlFile",
      "fileExtensions": [
        ".html"
      ],
      "contentTypes": [
        "text/html"
      ],
      "versions": []
    },
    {
      "format": "OpenXmlWord",
      "fileExtensions": [
        ".docx"
      ],
      "contentTypes": [
        "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
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