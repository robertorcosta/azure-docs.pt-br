---
title: OCR (reconhecimento óptico de caracteres)-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao OCR (reconhecimento óptico de caracteres) de imagens e documentos com texto impresso e manuscrito usando o API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e2226f70ed3318bb370f0afee003fd9f91153a45
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167854"
---
# <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

O API da Pesquisa Visual Computacional do Azure inclui recursos de OCR (reconhecimento óptico de caracteres) que extraem texto impresso ou manuscrito de imagens. Você pode extrair texto de imagens, como fotos de chapas de licença ou contêineres com números de série, bem como de documentos-faturas, faturas, relatórios financeiros, artigos e muito mais. 

## <a name="read-api"></a>API de leitura 

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) do pesquisa Visual computacional é a tecnologia de OCR mais recente do Azure que extrai o texto impresso (em vários idiomas), texto manuscrito (somente em inglês), dígitos e símbolos de moeda de imagens e documentos PDF de várias páginas. Ele é otimizado para extrair texto de imagens com texto pesado e documentos PDF de várias páginas com idiomas mistos. Ele dá suporte à detecção de texto impresso e manuscrito na mesma imagem ou documento.

![Como o OCR converte imagens e documentos em uma saída estruturada com texto extraído](./Images/how-ocr-works.svg)

A API de leitura fornece recursos de OCR por meio de duas operações- **ler** e **obter resultados de leitura**.

## <a name="the-read-operation"></a>A operação de leitura

A [operação de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) usa um documento de imagem ou PDF como entrada e extrai o texto de forma assíncrona. A chamada retorna com um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID da operação a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | https://cognitiveservice/vision/v3.0-preview/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f |

## <a name="the-get-read-results-operation"></a>A operação obter resultados da leitura

A segunda etapa é chamar a operação [obter resultados de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Essa operação usa como entrada a ID da operação que foi criada pela operação de leitura. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. Você chama essa operação iterativamente até que ela retorne com o valor **Succeeded** . Use um intervalo de 1 a 2 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

|Campo| Type | Valores possíveis |
|:-----|:----:|:----|
|status | string | não iniciado: a operação não foi iniciada. |
| |  | em execução: a operação está sendo processada. |
| |  | falha: a operação falhou. |
| |  | êxito: a operação foi bem-sucedida. |

> [!NOTE]
> A camada gratuita limita a taxa de solicitação a 20 chamadas por minuto. A camada paga permite 10 solicitações por segundo (RPS) que podem ser aumentadas mediante solicitação. Use o canal de suporte do Azure ou sua equipe de conta para solicitar uma taxa mais alta de solicitação por segundo (RPS).

Quando o campo **status** tiver o valor **êxito** , a resposta JSON conterá o conteúdo de texto extraído de sua imagem ou documento. A resposta JSON mantém os agrupamentos de linhas originais de palavras reconhecidas. Ele inclui as linhas de texto extraídas e suas coordenadas de caixa delimitadora. Cada linha de texto inclui todas as palavras extraídas com suas coordenadas e pontuações de confiança.

### <a name="sample-json-output"></a>Saída JSON de exemplo

Consulte o exemplo a seguir de uma resposta JSON bem-sucedida:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Siga o guia de início rápido de [extrair texto impresso e manuscrito](./QuickStarts/CSharp-hand-text.md) para implementar o OCR usando C# e a API REST.

## <a name="input-requirements"></a>Requisitos de entrada

As imagens e os documentos de entrada têm os seguintes requisitos:
* Formatos de arquivo com suporte: JPEG, PNG, BMP, PDF e TIFF
* Para PDF e TIFF, até 2000 páginas são processadas. Para assinantes de camada gratuita, somente as duas primeiras páginas são processadas.
* O tamanho do arquivo deve ser menor que 50 MB e dimensões, pelo menos, 50 x 50 pixels e no máximo 10000 x 10000 pixels.
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondendo a tamanhos de papel ofício ou a3 e menores.

> [!NOTE]
> **Entrada de idioma** 
>
> A [operação de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) tem um parâmetro de solicitação opcional para o idioma. Esse é o código de idioma BCP-47 do texto no documento. A leitura dá suporte à identificação automática de idioma e a documentos multilíngues, portanto, forneça apenas um código de idioma se desejar forçar o processamento do documento como aquele idioma específico.

## <a name="language-support"></a>Suporte ao idioma

### <a name="printed-text"></a>Texto impresso
A [API Read 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) dá suporte à extração de texto impresso em idiomas inglês, espanhol, alemão, francês, italiano, Português e holandês. 

A [Visualização pública da API de leitura 3,1](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) adiciona suporte para chinês simplificado. Se o seu cenário exigir suporte a mais idiomas, consulte a seção [API de OCR](#ocr-api) . 

Consulte os [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) para obter a lista completa de idiomas com suporte para OCR.

### <a name="handwritten-text"></a>Texto manuscrito
Atualmente, a operação de leitura dá suporte à extração de texto manuscrito exclusivamente em inglês.

## <a name="integration-options"></a>Opções de integração

### <a name="use-the-rest-api-or-client-sdk"></a>Usar a API REST ou o SDK do cliente
A [API REST de leitura 3. x](./QuickStarts/CSharp-hand-text.md) é a opção preferida para a maioria dos clientes devido à facilidade de integração e produtividade rápida pronta para uso. O Azure e o serviço de Pesquisa Visual Computacional tratam das necessidades de escala, desempenho, segurança de dados e conformidade enquanto você se concentra em atender às necessidades dos seus clientes.

### <a name="use-containers-for-on-premise-deployment"></a>Usar contêineres para implantação local
O [contêiner do docker 2,0 de leitura (versão prévia)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) permite que você implante os novos recursos de OCR em seu próprio ambiente local. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados.

## <a name="read-ocr-examples"></a>Ler exemplos de OCR

### <a name="text-from-images"></a>Texto de imagens

A seguinte saída da API de leitura mostra o texto extraído de uma imagem com diferentes ângulos, cores e fontes de texto.

![Uma imagem de várias palavras em diferentes cores e ângulos, com texto extraído listado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto de documentos

A API de leitura também pode colocar documentos em PDF como entrada.

![Um documento de nota fiscal, com texto extraído listado](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Texto manuscrito

A operação de leitura extrai texto manuscrito de imagens (atualmente somente em inglês).

![Uma imagem de uma anotação manuscrita, com texto extraído listado](./Images/handwritten-example.png)

### <a name="printed-text"></a>Texto impresso

A operação de leitura pode extrair texto impresso em vários idiomas diferentes.

![Uma imagem de um padrão em espanhol, com texto extraído listado](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Documentos em idiomas mistos

A API de leitura dá suporte a imagens e documentos que contêm várias linguagens diferentes, normalmente conhecidas como documentos de idioma misto. Ele funciona classificando cada linha de texto do documento no idioma detectado antes de extrair seu conteúdo de texto.

![Uma imagem de frases em várias linguagens, com o texto extraído listado](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>API DE OCR

A [API de OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) usa um modelo de reconhecimento mais antigo, dá suporte apenas a imagens e é executada de forma síncrona, retornando imediatamente com o texto detectado. Consulte os [idiomas com suporte para OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) e, em seguida, ler API.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam os serviços de leitura/OCR devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a página serviços cognitivas na [central de confiabilidade da Microsoft](https://www.microsoft.com/trust-center/product-overview) para saber mais.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [API REST de leitura 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API REST de visualização pública 3,1 de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) com suporte adicional para chinês simplificado.
- Siga o guia de início rápido [extrair texto](./QuickStarts/CSharp-hand-text.md) para implementar o OCR usando C#, Java, JavaScript ou Python juntamente com a API REST.
