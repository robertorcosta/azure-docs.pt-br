---
title: IDs – reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos relacionados à extração de dados de documentos de identidade com a API de IDs predefinidas do reconhecedor de formulário.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 01a73e7940f88a3eb6e040f26d255448294cab18
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467778"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Modelo do cartão de identificação predefinida (ID) do reconhecedor de formulário

O reconhecedor de formulários do Azure pode analisar e extrair informações de IDs (cartões de identificação) do governo usando seu modelo de IDs predefinido. Ele combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/concept-recognizing-text.md) com recursos de reconhecimento de ID para extrair informações importantes de passaportes mundiais e licenças do driver dos EUA (todos os Estados 50 e D.C.). A API IDs extrai informações de chave desses documentos de identidade, como nome, sobrenome, data de nascimento, número do documento e muito mais. Essa API está disponível no formulário reconhecedor v 2.1 Preview como um serviço de nuvem e como um contêiner local.

## <a name="what-does-the-id-service-do"></a>O que o serviço de ID faz? 

O serviço de IDs predefinidos extrai os valores de chave de passaportes mundiais e licenças do driver dos EUA e os retorna em uma resposta de JSON estruturado organizado. 

![Licença do driver de exemplo](./media/id-example-drivers-license.JPG)

![Exemplo de passaporte](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Tipo | Descrição | Valor | 
|:-----|:----|:----|:----|
|  País | country | Código do país em conformidade com o padrão ISO 3166 | NOS | 
|  DateOfBirth | date | DOB no formato AAAA-MM-DD | "1980-01-01" | 
|  DateOfExpiration | date | Data de validade no formato AAAA-MM-DD | "2019-05-05" |  
|  DocumentNumber | string | Número de passaporte relevante, número de licença do driver, etc. | "340020013" |  
|  FirstName | string | Nome e inicial do meio extraídos, se aplicável | Jennifer | 
|  LastName | string | Sobrenome extraído | "BROOKS" |   
|  Nacionalidade | country | Código do país em conformidade com o padrão ISO 3166 | NOS |
|  Sexo | gender | Os valores extraídos possíveis incluem "M", "F" e "X" | "F" | 
|  MachineReadableZone | objeto | MRZ do Passport extraído incluindo duas linhas de 44 caracteres cada | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<<<<<<<<<<<<<<<<<<<<<<< 6 715816" |
|  DocumentType | string | Tipo de documento, por exemplo, Passport, licença do driver | passaporte |  
|  Endereço | string | Endereço extraído (somente licença do driver) | "RUA 123 ENDEREÇO DE SUA CIDADE WA 99999-1234"|
|  Região | string | Região extraída, estado, província, etc. (somente licença do driver) | Paulo | 

### <a name="additional-features"></a>Recursos adicionais

A API IDs também retorna as seguintes informações:

* Nível de confiança de campo (cada campo retorna um valor de confiança associado)
* Texto bruto de OCR (saída de texto extraído por OCR para o recebimento inteiro)
* Caixa delimitadora de cada campo extraído nas licenças do driver dos EUA
* Caixa delimitadora para MRZ (zona legível por computador) em passaportes

  > [!NOTE]
  > As IDs predefinidas não detectam a autenticidade da ID
  >
  > IDs predefinidas do reconhecedor de formulário extrai dados de chave de dados de ID. No entanto, ele não detecta a validade ou a autenticidade do documento de identidade original. 

## <a name="try-it-out"></a>Experimente

Para testar o formulário IDs do reconhecedor, vá para a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Tipos de ID com suporte  

* **IDs predefinidas v 2.1-Preview. 3** Extrai valores de chave de passaportes mundiais e licenças do driver dos EUA. 

  > [!NOTE]
  > Suporte ao tipo de ID 
  >
  > Atualmente, os tipos de ID com suporte incluem o passaporte mundial e as licenças do driver dos EUA. Estamos buscando ativamente expandir nosso suporte de ID para outros documentos de identidade em todo o mundo.

## <a name="post-analyze-id-document"></a>Documento de ID do POST Analyze

A operação [analisar ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) usa uma imagem ou um PDF de uma ID como entrada e extrai os valores de interesse. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>OBTER o resultado do documento de ID de análise

<!---
Need to update this with updated APIM links when available
-->

A segunda etapa é chamar a operação [**Get Analyze IdDocument Result**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de análise de ID. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação de análise não foi iniciada. |
| |  | em execução: a operação de análise está em andamento. |
| |  | falha: falha na operação de análise. |
| |  | êxito: a operação de análise foi bem-sucedida. |

Quando o campo **status** tiver o valor **êxito** , a resposta JSON incluirá os resultados de reconhecimento de texto e compreensão de recibo. Os resultados das IDs são organizados como um dicionário de valores de campo nomeados, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos correspondentes do Word. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

![resultados de recibo de exemplo](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Saída JSON de exemplo

Consulte o exemplo a seguir de uma resposta JSON bem-sucedida: o `readResults` nó contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. O `documentResults` nó contém os valores de ID que o modelo descobriu. Esse nó também é onde você encontrará pares úteis de chave/valor como nome, sobrenome, número do documento e muito mais.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Próximas etapas

- Experimente suas próprias IDs e exemplos na [interface do usuário de exemplo do reconhecedor de formulário](https://fott-preview.azurewebsites.net/).
- Conclua um [início rápido do reconhecedor de formulário](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de ID com o reconhecedor de formulário na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Consulte também

* [**O que é o Reconhecimento de Formulários?**](./overview.md)
* [**Documentos de referência da API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
