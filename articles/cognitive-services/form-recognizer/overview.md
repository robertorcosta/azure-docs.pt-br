---
title: O que é o Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: O Reconhecimento de Formulários dos Serviços Cognitivos do Azure permite identificar e extrair pares chave-valor e dados de tabela de documentos de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 91ea2b68828ac54d4128a90550e9c60e065b719d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379412"
---
# <a name="what-is-form-recognizer"></a>O que é o Reconhecimento de Formulários?

O Reconhecimento de Formulários do Azure é um serviço cognitivo que usa a tecnologia de aprendizado de máquina para identificar e extrair texto, pares chave-valor e dados de tabela de documentos de formulário. Em ingere texto de formulários e gera dados estruturados que incluem as relações no arquivo original. É possível obter resultados com rapidez e precisão, adaptados ao seu conteúdo específico, sem intervenção manual intensa nem ampla experiência em ciência de dados. O Reconhecimento de Formulários é composto por modelos personalizados, o modelo de recebimento predefinido e a API de layout. Chame os modelos do Reconhecimento de Formulários usando uma API REST para reduzir a complexidade e integrá-lo a seu fluxo de trabalho ou aplicativo.

O Reconhecimento de Formulários é composto pelos seguintes serviços:
* **Modelos personalizados** – extraia pares de chave/valor e dados de tabela de formulários. Esses modelos são treinados usando dados próprios e, portanto, são adaptados para seus formulários.
* **Modelo de recebimento predefinido** – extraia dados de recibos de vendas dos EUA usando um modelo predefinido.
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

## <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

O Reconhecimento de Formulários também inclui um modelo para ler os recibos de vendas em inglês do Estados Unidos&mdash;o tipo usado por restaurantes, companhias de gás, varejo etc. ([exemplo de recibo](./media/contoso-receipt-small.png)). Esse modelo extrai informações importantes, como a data e a hora da transação, informações de comerciantes, valores de impostos e totais e muito mais. Além disso, o modelo de recibo predefinido é treinado para reconhecer e retornar todo o texto de um recibo.

## <a name="layout-api"></a>API de layout

O Reconhecimento de Formulários também pode extrair a estrutura de texto e tabela (os números de linha e coluna associados ao texto) usando o OCR (reconhecimento óptico de caracteres) de alta definição. 

## <a name="where-do-i-start"></a>Por onde começo?

**Etapa 1:** Solicitar acesso:

O Reconhecimento de Formulários está disponível em uma versão prévia de acesso limitado. Para obter acesso à versão prévia, preencha e envie o formulário de [Solicitação de acesso ao Reconhecimento de Formulários](https://aka.ms/FormRecognizerRequestAccess). O formulário solicita informações sobre você, sua empresa e o cenário no qual usará o Reconhecimento de Formulários.

**Etapa 2:** Criar um recurso do Reconhecimento de Formulários no portal do Azure:

Quando o acesso para usar o Reconhecimento de Formulários for concedido, você receberá um email de boas-vindas com vários links e recursos. Use o link do "portal do Azure" na mensagem para abrir o portal do Azure e criar um recurso do Reconhecimento de Formulários.

**Etapa 3:** Extrair dados de seus formulários:

* Personalizado – treinar um modelo para seus formulários
  * Treinar sem rótulos
    * [Início Rápido: treinar em um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o cURL](quickstarts/curl-train-extract.md)
    * [Início Rápido: treinar em um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o Python](quickstarts/python-train-extract.md)
  * Treinar com rótulos 
    * [Treinar um modelo de Reconhecimento de Formulários com rótulos usando a ferramenta de rotulagem de exemplo](quickstarts/label-tool.md)
    * [Treinar um modelo do Reconhecimento de Formulários com rótulos usando a API REST e o Python](quickstarts/python-labeled-data.md) 
* Confirmações predefinidas – extrair dados de recibos de vendas dos EUA
  * [Início Rápido: extrair dados de recibos usando cURL](quickstarts/curl-receipts.md)
  * [Início Rápido: extrair dados de recibos usando Python](quickstarts/python-receipts.md)
* Layout – extrair a estrutura de tabela e texto de formulários
  * [Início Rápido: Extrair dados de layout usando o Python](quickstarts/python-layout.md)

Recomendamos usar o serviço gratuito quando estiver aprendendo a tecnologia. Lembre-se de que o número de páginas gratuitas é limitado a 500 por mês.

**Etapa 4:** Examinar as APIs REST:

Você usará as APIs a seguir para treinar modelos e extrair dados estruturados de formulários.

|Nome |DESCRIÇÃO |
|---|---|
| **Treinar Modelo Personalizado**| Treine um novo modelo para analisar seus formulários usando cinco formulários do mesmo tipo. Defina o parâmetro _useLabelFile_ como `true` para treinar usando dados rotulados manualmente. |
| **Analisar Formulário** |Analise um documento individual transmitido como um fluxo para extrair texto, pares chave-valor e tabelas do formulário com o modelo personalizado.  |
| **Analisar Recibo** |Analise um único documento de recibo para extrair informações importantes e outros textos do recibo.|
| **Analisar Layout** |Analise o layout de um formulário para extrair a estrutura de tabela e texto.|

Explore a [documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) para saber mais. Se você estiver familiarizado com uma versão anterior da API, confira o artigo [O que há de novo](./whats-new.md) para saber mais sobre as alterações recentes.

## <a name="input-requirements"></a>Requisitos de entrada
### <a name="custom-model"></a>Modelo personalizado

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Modelo de recebimento predefinido

Os requisitos de entrada para o modelo de recebimento são um pouco diferentes.

* O formato deve ser JPEG, PNG, BMP, PDF (texto ou digitalizado) ou TIFF.
* O tamanho do arquivo deve ser inferior a 20 MB.
* As imagens de dimensões devem estar entre 50 x 50 pixels e 10.000 x 10.000 pixels. 
* As dimensões de PDF devem ter no máximo 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou A3 e menores.
* Para PDF e TIFF, somente as primeiras 200 páginas são processadas (com uma assinatura de camada gratuita, somente as duas primeiras páginas são processadas).

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

O serviço é oferecido como uma [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de um serviço do Azure de acordo com os [Termos de Serviço Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Assim como ocorre com todos os serviços cognitivos, os desenvolvedores que usam o serviço Reconhecimento de Formulários devem estar cientes das políticas da Microsoft referentes aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Complete um [início rápido](quickstarts/curl-train-extract.md) para começar a usar as [APIs do Reconhecimento de Formulários](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
