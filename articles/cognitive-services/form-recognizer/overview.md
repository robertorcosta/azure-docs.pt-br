---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: O Reconhecimento de Formulários dos Serviços Cognitivos do Azure permite identificar e extrair pares chave-valor e dados de tabela de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 070796cd260e56bb51115a7ef33ced8455bfb6a9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394390"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Reconhecimento de Formulários do Azure é um serviço cognitivo que usa a tecnologia de aprendizado de máquina para identificar e extrair texto, pares chave-valor e dados de tabela de documentos de formulário. Em ingere texto de formulários e gera dados estruturados que incluem as relações no arquivo original. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados. O Reconhecimento de Formulários é composto por modelos personalizados, o modelo de recebimento predefinido e a API de layout. Chame os modelos do Reconhecimento de Formulários usando uma API REST para reduzir a complexidade e integrá-lo a seu fluxo de trabalho ou aplicativo.

O Reconhecimento de Formulários é composto pelos seguintes serviços:
* **Modelos personalizados** – extraia pares de chave/valor e dados de tabela de formulários. Esses modelos são treinados usando dados próprios e, portanto, são adaptados para seus formulários.
* **Modelos predefinidos**: extraia dados de tipos de formulário exclusivos usando modelos predefinidos. Os modelos predefinidos estão atualmente disponíveis para recibos de vendas e cartões de visita em inglês.
* **API de layout** – extraia de documentos estruturas de texto e tabela juntamente com suas coordenadas de caixa delimitadora.

<!-- add diagram -->

## <a name="custom-models"></a>Modelos personalizados

O modelo personalizado do Reconhecimento de Formulários treina para dados próprios e você só precisa de cinco formulários de entrada de exemplo para começar. Um modelo treinado pode gerar dados estruturados que incluem as relações no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

Você tem as seguintes opções ao treinar modelos personalizados: treinamento usando dados rotulados e sem dados rotulados.

### <a name="train-without-labels"></a>Treinar sem rótulos

Por padrão, o Reconhecimento de Formulários usa o aprendizado não supervisionado para entender o layout e as relações entre campos e entradas em seus formulários. Quando você envia seus formulários de entrada, o algoritmo agrupa os formulários por tipos, descobre quais chaves e tabelas estão presentes e associa valores a chaves e entradas a tabelas. Isso não exige a rotulagem manual de dados nem a codificação e a manutenção intensivas. Recomendamos que você experimente esse método primeiro.

### <a name="train-with-labels"></a>Treinar com rótulos

Quando você treina os dados rotulados, o modelo supervisiona o aprendizado para extrair os valores de interesse usando os formulários rotulados que você fornece. Isso resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.

O Reconhecimento de Formulários usa a [API de layout](#layout-api) para aprender os tamanhos e as posições esperados de elementos de texto impressos e manuscritos. Em seguida, ele usa rótulos especificados pelo usuário para aprender as associações de chave/valor nos documentos. Recomendamos que você use cinco formulários rotulados manualmente do mesmo tipo para começar ao treinar um novo modelo e adicionar mais dados rotulados conforme necessário para aprimorar a precisão do modelo.

## <a name="prebuilt-models"></a>Modelos predefinidos

O Reconhecimento de Formulários também inclui modelos predefinidos para tipos de formulário exclusivos.

### <a name="prebuilt-receipt-model"></a>Modelo de Recibo Predefinido
O modelo de Recibo Predefinido é usado para a leitura de recibos de vendas em inglês da Austrália, do Canadá, da Grã-Bretanha, da Índia e dos Estados Unidos: o tipo usado por restaurantes, postos de gasolina, varejo etc. Esse modelo extrai informações essenciais, como data e hora da transação, informações do comerciante, valores de impostos, itens de linha, totais, entre outros. Além disso, o modelo de recibo predefinido é treinado para reconhecer e retornar todo o texto de um recibo. 

![exemplo de recibo](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Modelo de Cartões de Visita Predefinidos
O modelo de Cartões de Visita permite que você extraia informações, como nome da pessoa, cargo, endereço, email, empresa e números de telefone de cartões de visita em inglês. 

![exemplo de cartão de visita](./media/business-card-english.jpg)

## <a name="layout-api"></a>API de layout

O Reconhecimento de Formulários também pode extrair a estrutura de texto e tabela (os números de linha e coluna associados ao texto) usando o OCR (reconhecimento óptico de caracteres) de alta definição.

## <a name="get-started"></a>Introdução

Siga um início rápido para começar a extrair dados de seus formulários. Recomendamos usar o serviço gratuito quando estiver aprendendo a tecnologia. Lembre-se de que o número de páginas gratuitas é limitado a 500 por mês.

* [Início rápido da biblioteca de cliente](./quickstarts/client-library.md) (todos os idiomas, vários cenários)
* Inícios rápidos da interface do usuário da Web
  * [Treinar com rótulos – ferramenta de rotulação de amostra](quickstarts/label-tool.md)
* Guias de início rápido do REST
  * Treinar modelos personalizados e extrair dados de formulário
    * [Treinar sem rótulos – cURL](quickstarts/curl-train-extract.md)
    * [Treinar sem rótulos – Python](quickstarts/python-train-extract.md)
    * [Treinar com rótulos – Python](quickstarts/python-labeled-data.md)
  * Extrair dados de recibos de vendas
    * [Extrair dados de recibo – cURL](quickstarts/curl-receipts.md)
    * [Extrair dados de recibo – Python](quickstarts/python-receipts.md)
  * Extrair dados de cartões de visita
    * [Extrair dados do cartão de visita – Python](quickstarts/python-business-cards.md)
  * Extrair a estrutura de tabela e texto de formulários
    * [Extrair dados de layout – Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>revisar as Especificações das APIs REST

Você usará as APIs a seguir para treinar modelos e extrair dados estruturados de formulários.

|Nome |Descrição |
|---|---|
| **Treinar Modelo Personalizado**| Treine um novo modelo para analisar seus formulários usando cinco formulários do mesmo tipo. Defina o parâmetro _useLabelFile_ como `true` para treinar usando dados rotulados manualmente. |
| **Analisar Formulário** |Analise um documento individual transmitido como um fluxo para extrair texto, pares chave-valor e tabelas do formulário com o modelo personalizado.  |
| **Analisar Recibo** |Analise um único documento de recibo para extrair informações importantes e outros textos do recibo.|
| **Analisar o Cartão de Visita** |Analise um cartão de visita para extrair informações e texto importantes.|
| **Analisar Layout** |Analise o layout de um formulário para extrair a estrutura de tabela e texto.|

Explore a [documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>Predefinida

Os requisitos de entrada para o modelo de recebimento são um pouco diferentes.

* O formato deve ser JPEG, PNG, PDF (texto ou digitalizado) ou TIFF.
* O tamanho do arquivo deve ser inferior a 20 MB.
* As imagens de dimensões devem estar entre 50 x 50 pixels e 10.000 x 10.000 pixels.
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou A3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os serviços cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).