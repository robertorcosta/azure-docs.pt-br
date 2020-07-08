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
ms.openlocfilehash: d1c642a660b24cfc54c9c4308b8956582e13d50a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954733"
---
# <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

O Pesquisa Visual Computacional inclui novos recursos de OCR (reconhecimento ótico de caracteres ópticos) baseados em aprendizado profundo que extraem texto impresso ou manuscrito de imagens e documentos em PDF. Pesquisa Visual Computacional extrai texto de documentos analógicos (imagens, documentos digitalizados) e documentos digitalizados. Você pode extrair texto de imagens no mesmo, como fotos de chapas de licença ou contêineres com números de série, bem como de documentos-faturas, títulos, relatórios financeiros, artigos e muito mais. Essa funcionalidade de OCR está disponível como parte do serviço gerenciado na nuvem ou localmente (contêineres). Além disso, ele dá suporte a redes virtuais e pontos de extremidade privados para atender às suas necessidades de privacidade e conformidade de nível empresarial.

## <a name="read-api"></a>API de leitura 

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) do pesquisa Visual computacional é a tecnologia de OCR mais recente da Microsoft que extrai texto impresso (sete idiomas), texto manuscrito (somente em inglês), dígitos e símbolos de moeda de imagens e documentos PDF de várias páginas. Ele é otimizado para extrair texto de imagens de texto intenso e documentos em PDF com várias páginas com idiomas mistos. Ele dá suporte à detecção de texto impresso e manuscrito (somente em inglês) na mesma imagem ou documento. A lista completa de idiomas com suporte pode ser encontrada na página [suporte de idioma para pesquisa Visual computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) .

### <a name="how-it-works"></a>Como funciona

A [API de leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) dá suporte a documentos com texto pesado de até 2000 páginas e, portanto, é executada de forma assíncrona. A primeira etapa é chamar a operação de leitura. A operação de leitura usa um documento de imagem ou PDF como entrada e retorna uma ID de operação. 

A segunda etapa é chamar a operação [obter resultados](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Essa operação usa a ID da operação que foi criada pela operação de leitura. Em seguida, ele retorna o conteúdo de texto extraído de sua imagem ou documento na forma de JSON. A resposta JSON mantém os agrupamentos de linhas originais de palavras reconhecidas. Ele inclui as linhas de texto extraídas e suas coordenadas de caixa delimitadora. Cada linha de texto inclui todas as palavras extraídas com suas coordenadas e uma pontuação de confiança.

Se necessário, a leitura corrige a rotação da página reconhecida retornando o deslocamento rotacional em graus sobre o eixo horizontal da imagem, como mostrado na ilustração a seguir.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/vision-overview-ocr-read.png)

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

A API de leitura dá suporte à extração de texto impresso nos idiomas inglês, espanhol, alemão, francês, italiano, Português e holandês. Se seu cenário exigir suporte a mais idiomas, consulte a visão geral da API de OCR neste documento. Consulte a lista de todos os [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Suporte a idiomas mistos

A API de leitura dá suporte a imagens e documentos com vários idiomas, comumente conhecidos como documentos de idioma misto. Ele faz isso classificando cada linha de texto do documento no idioma detectado antes de extrair o conteúdo do texto.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como acontece com todos os serviços cognitivas, os desenvolvedores que usam o serviço de leitura devem estar cientes das políticas da Microsoft nos dados do cliente. Consulte a página serviços cognitivas na [central de confiabilidade da Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview) para saber mais.

### <a name="deploy-on-premises"></a>Implantar no local

Ler também está disponível como um contêiner do Docker (versão prévia) para permitir que você implante os novos recursos de OCR em seu próprio ambiente. Os contêineres são ótimos para requisitos específicos de segurança e governança de dados. Consulte [como instalar e executar os contêineres de leitura.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>API DE OCR

A [API de OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) usa um modelo de reconhecimento mais antigo, dá suporte apenas a imagens e é executada de forma síncrona, retornando imediatamente com o texto detectado. Ele dá suporte a [mais idiomas](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) do que a API de leitura.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [API REST de leitura 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Siga o guia de início rápido [extrair texto](./QuickStarts/CSharp-hand-text.md) para implementar o OCR usando C#, Java, JavaScript ou Python juntamente com a API REST.
