---
title: OCR (reconhecimento óptico de caracteres)-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao OCR (reconhecimento óptico de caracteres) de imagens e documentos com texto impresso e manuscrito usando o API da Pesquisa Visual Computacional.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 2b3f9b0a4bec76f1f5f9b1f42ec33fdf5e2678bf
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760151"
---
# <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

A API da Pesquisa Visual Computacional da Microsoft inclui recursos de OCR (reconhecimento óptico de caracteres) que extraem texto impresso ou manuscrito de imagens e documentos em PDF. As APIs de OCR extraem o texto de documentos analógicos (imagens, documentos digitalizados) e documentos digitalizados. Você pode extrair texto de imagens no mesmo, como fotos de chapas de licença ou contêineres com números de série, bem como de documentos-faturas, títulos, relatórios financeiros, artigos e muito mais. A nova API de OCR de leitura está disponível como parte do serviço gerenciado na nuvem ou localmente (contêineres). Além disso, ele dá suporte a redes virtuais e pontos de extremidade privados para atender às suas necessidades de privacidade e conformidade de nível empresarial.

## <a name="read-api"></a>API de leitura 

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) do pesquisa Visual computacional é a tecnologia de OCR mais recente da Microsoft que extrai o texto impresso em vários idiomas, texto manuscrito (somente em inglês), dígitos e símbolos de moeda de imagens e documentos em PDF com várias páginas. Ele é otimizado para extrair texto de imagens de texto intenso e documentos em PDF com várias páginas com idiomas mistos. Ele dá suporte à detecção de texto impresso e manuscrito (somente em inglês) na mesma imagem ou documento. Consulte a lista completa de [idiomas com suporte para OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) .

### <a name="how-ocr-works"></a>Como funciona o OCR

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) dá suporte a documentos com texto pesado de até 2000 páginas e, portanto, é executada de forma assíncrona. A primeira etapa é chamar a operação de leitura. A operação de leitura usa um documento de imagem ou PDF como entrada e retorna uma ID de operação. 

A segunda etapa é chamar a operação [obter resultados](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Essa operação usa a ID da operação que foi criada pela operação de leitura. Em seguida, ele retorna o conteúdo de texto extraído de sua imagem ou documento na forma de JSON. A resposta JSON mantém os agrupamentos de linhas originais de palavras reconhecidas. Ele inclui as linhas de texto extraídas e suas coordenadas de caixa delimitadora. Cada linha de texto inclui todas as palavras extraídas com suas coordenadas e pontuações de confiança.

Se necessário, a leitura corrige a rotação da página reconhecida retornando o deslocamento rotacional em graus sobre o eixo horizontal da imagem, como mostrado na ilustração a seguir.

![Como o OCR converte imagens e documentos em uma saída estruturada com texto extraído](./Images/how-ocr-works.svg)

### <a name="sample-ocr-output"></a>Saída de OCR de exemplo

Uma resposta bem-sucedida é retornada no formato JSON, conforme mostrado no exemplo a seguir:

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

### <a name="input-requirements"></a>Requisitos de entrada

A API de leitura usa as seguintes entradas:
* Formatos de arquivo com suporte: JPEG, PNG, BMP, PDF e TIFF
* Para PDF e TIFF, até 2000 páginas são processadas. Para assinantes de camada gratuita, somente as duas primeiras páginas são processadas.
* O tamanho do arquivo deve ser menor que 50 MB e dimensões, pelo menos, 50 x 50 pixels e no máximo 10000 x 10000 pixels.
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondendo a tamanhos de papel ofício ou a3 e menores.

### <a name="text-from-images"></a>Texto de imagens

A seguinte saída da API de leitura mostra as linhas e palavras de texto extraídas de uma imagem com texto em diferentes ângulos, cores e fontes

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Texto de documentos

Além das imagens, a API de leitura usa um documento PDF como entrada.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Texto manuscrito em inglês

Agora, a operação de leitura dá suporte à extração de texto manuscrito exclusivamente em inglês.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Texto impresso em idiomas com suporte

A API Read 3,0 dá suporte à extração de texto impresso em idiomas inglês, espanhol, alemão, francês, italiano, Português e holandês. [Leia 3,1 API de visualização pública](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) adiciona suporte para chinês simplificado. Se seu cenário exigir suporte a mais idiomas, consulte a visão geral da API de OCR neste documento. Consulte a lista de todos os [idiomas com suporte OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr)

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Suporte a idiomas mistos

A API de leitura dá suporte a imagens e documentos com vários idiomas, comumente conhecidos como documentos de idioma misto. Ele faz isso classificando cada linha de texto do documento no idioma detectado antes de extrair o conteúdo do texto.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de leitura devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a página serviços cognitivas na [central de confiabilidade da Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview) para saber mais.

### <a name="containers-for-on-premise-deployment"></a>Contêineres para implantação local

Ler também está disponível como um contêiner do Docker (versão prévia) para permitir que você implante os novos recursos de OCR em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Consulte [como instalar e executar os contêineres de leitura.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>API DE OCR

A [API de OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) usa um modelo de reconhecimento mais antigo, dá suporte apenas a imagens e é executada de forma síncrona, retornando imediatamente com o texto detectado. Consulte os [idiomas com suporte para OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) do que a API de leitura.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [API REST de leitura 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API REST de visualização pública 3,1 de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) com suporte adicional para chinês simplificado.
- Siga o guia de início rápido [extrair texto](./QuickStarts/CSharp-hand-text.md) para implementar o OCR usando C#, Java, JavaScript ou Python juntamente com a API REST.
