---
title: Layouts-reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de layout com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467010"
---
# <a name="form-recognizer-layout-service"></a>Serviço de layout do reconhecedor de formulário

O reconhecedor de formulários do Azure pode extrair texto, tabelas, marcas de seleção e informações de estrutura de documentos usando seu serviço de layout. A API de layout permite que os clientes consigam documentos em uma variedade de formatos e retornem representações de dados estruturados dos documentos. Ele combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/concept-recognizing-text.md) com modelos de aprendizado profundo para extrair texto, tabelas, marcas de seleção e estrutura do documento. 

## <a name="what-does-the-layout-service-do"></a>O que o serviço de layout faz?

A API de layout extrai texto, tabelas, marcas de seleção e informações de estrutura de documentos com precisão excepcional e retorna uma resposta organizada, estruturada e JSON. Os documentos podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API de layout extrairá com precisão a saída estruturada de todos esses documentos.

![Exemplo de layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de layout do reconhecedor de formulário, vá para a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Formulário ferramenta de teste de OCR (FOTT)](https://fott-preview.azurewebsites.net)

Você precisará de uma assinatura do Azure ([crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto de extremidade de [recurso do reconhecedor de formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e a chave para testar a API de layout do reconhecedor de formulário. 

![Captura de tela de exemplo; o texto, as tabelas e as marcas de seleção de um documento são analisados](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>A operação de análise de layout

Primeiro, chame a operação [analisar layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) . O layout de análise usa um documento (imagem, TIFF ou arquivo PDF) como a entrada e extrai o texto, as tabelas, as marcas de seleção e a estrutura do documento. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Saída da ordem de leitura natural (somente latino)

Você pode especificar a ordem na qual as linhas de texto são geradas com o `readingOrder` parâmetro de consulta. Use `natural` para uma saída de ordem de leitura mais amigável, conforme mostrado no exemplo a seguir. Esse recurso só tem suporte em idiomas latinos.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de leitura de layout" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Selecionar números de página ou intervalos para extração de texto

Para grandes documentos de várias páginas, use o `pages` parâmetro de consulta para indicar números de página ou intervalos de página específicos para a extração de texto. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos – todas as páginas (1-10) e páginas selecionadas (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas de layout":::

## <a name="the-get-analyze-layout-result-operation"></a>A operação obter resultado do layout de análise

A segunda etapa é chamar a operação [Get Analyze layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de análise de layout. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. 

|Campo| Type | Valores possíveis |
|:-----|:----:|:----|
|status | string | `notStarted`: A operação de análise não foi iniciada.<br /><br />`running`: A operação de análise está em andamento.<br /><br />`failed`: Falha na operação de análise.<br /><br />`succeeded`: A operação de análise foi bem-sucedida.|

Chame essa operação iterativamente até que ela retorne o `succeeded` valor. Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

Quando o campo **status** tiver o `succeeded` valor, a resposta JSON incluirá o layout extraído, o texto, as tabelas e as marcas de seleção. Os dados extraídos incluem linhas e palavras de texto extraídas, caixas delimitadas, aparência de texto com indicação manuscrita, tabelas e marcas de seleção com selecionado/não selecionado indicado. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificação manuscrita para linhas de texto (somente latino)

A resposta inclui classificar se cada linha de texto é do estilo de manuscrito ou não, juntamente com uma pontuação de confiança. Esse recurso só tem suporte em idiomas latinos. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="exemplo de classificação de manuscrito":::

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta para a operação *obter resultado do layout de análise* é uma representação estruturada do documento com todas as informações extraídas. Consulte aqui para obter um [arquivo de documento de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e sua saída de layout de [exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)de saída estruturada.

A saída JSON tem duas partes:

* `readResults` o nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* `pageResults` o nó contém as tabelas e células extraídas com suas caixas delimitadoras, confiança e uma referência às linhas e palavras em "readResults".

## <a name="example-output"></a>Saída de exemplo

### <a name="text"></a>Texto

A API de layout extrai texto de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP) com várias cores e ângulos de texto. Ele aceita fotos de documentos, faxes, texto impresso e/ou manuscrito (somente em inglês) e modos mistos. O texto é extraído com informações fornecidas em linhas, palavras, caixas delimitadoras, pontuações de confiança e estilo (manuscrito ou outro). Todas as informações de texto são incluídas na `readResults` seção da saída JSON. 

### <a name="tables"></a>Tabelas

A API de layout extrai tabelas de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP). Os documentos podem ser digitalizados, fotografados ou digitalizados. As tabelas podem ser complexas com células ou colunas mescladas, com ou sem bordas, e com ângulos ímpares. As informações da tabela extraída incluem o número de colunas e linhas, intervalo de linhas e span de coluna. Cada célula é extraída com sua caixa delimitadora e referência ao texto extraído na `readResults` seção. As informações de tabela estão localizadas na `pageResults` seção da saída JSON. 

![Exemplo de tabelas](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marcas de seleção

A API de layout também extrai marcas de seleção de documentos. As marcas de seleção extraídas incluem a caixa delimitadora, a confiança e o estado (selecionado/não selecionado). As informações de marca de seleção são extraídas na `readResults` seção da saída JSON. 

## <a name="next-steps"></a>Próximas etapas

* Experimente sua própria extração de layout usando a [ferramenta de exemplo do reconhecedor de formulário](https://fott-preview.azurewebsites.net/)
* Conclua um [início rápido do reconhecedor de formulário](quickstarts/client-library.md) para começar a extrair layouts na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
