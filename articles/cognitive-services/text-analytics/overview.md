---
title: O que é a API de Análise de Texto? - Recursos -
titleSuffix: Azure Cognitive Services
description: Use a API de Análise de Texto dos Serviços Cognitivos do Azure para análise de sentimento, extração de frases-chave, detecção de idioma e reconhecimento de entidade.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/07/2019
ms.author: aahi
ms.openlocfilehash: 66bc1e5441210b8d9153812c826643159fe53d50
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251323"
---
# <a name="what-is-the-text-analytics-api"></a>O que é a API de Análise de Texto?

A API de Análise de Texto é um serviço baseado em nuvem que fornece um processamento em idioma natural avançado de texto bruto e inclui quatro funções principais: análise de sentimento, extração de frases-chave, detecção de idioma e reconhecimento de entidade nomeada.

A API faz parte dos [Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/), uma coleção de algoritmos de IA e aprendizado de máquina na nuvem para seus projetos de desenvolvimento.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Análise de texto pode significar coisas diferentes, mas, em Serviços Cognitivos, a API de Análise de Texto oferece quatro tipos de análise conforme descrito na tabela a seguir. Use esses recursos com a [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) ou a [biblioteca de clientes](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Análise de Sentimento
[Análise de Sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md) pode ser usada para descobrir o que os clientes pensam da marca ou tópico, analisando texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva.<br /> Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e pontuar qualquer texto bruto que você forneça, retornando diretamente os resultados ao aplicativo responsável pela chamada.

## <a name="key-phrase-extraction"></a>Extração de Frases-Chave
[Extraia frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) automaticamente para identificar rapidamente os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".

## <a name="language-detection"></a>Detecção de Idioma
Você pode [detectar em qual idioma o texto de entrada está escrito](how-tos/text-analytics-how-to-language-detection.md) e relatar um código de idioma único para cada documento enviado na solicitação em uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação.

## <a name="named-entity-recognition"></a>Reconhecimento de Entidade Nomeada
[Identifique e categorize entidades](how-tos/text-analytics-how-to-entity-linking.md) em seu texto como pessoas, locais, organizações, data/hora, quantidades, percentuais, moedas e muito mais. Entidades conhecidas também são reconhecidas e vinculadas a mais informações na Web.

## <a name="use-containers"></a>Usar contêineres

[Use os contêineres de Análise de Texto](how-tos/text-analytics-how-to-install-containers.md) para extrair frases-chave, detectar idiomas e analisar o sentimento localmente por meio da instalação de contêineres do Docker padronizados mais próximos aos seus dados.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: você envia dados para análise e manipula as saídas no seu código. Os analisadores são consumidos como estão, sem nenhuma configuração ou personalização adicional.

1. [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para Análise de Texto. Posteriormente, [obtenha a chave](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gerada para autenticar suas solicitações.

2. [Formular uma solicitação](how-tos/text-analytics-how-to-call-api.md#json-schema) contendo seus dados como texto bruto não estruturado, em JSON.

3. Poste a solicitação no ponto de extremidade estabelecido durante a inscrição, acrescentando o recurso desejado: análise de sentimento, extração de frases-chave, detecção de idioma ou reconhecimento de entidade nomeada.

4. Transmita por streaming ou armazene a resposta localmente. Dependendo da solicitação, os resultados são uma pontuação de sentimento, uma coleção de frases-chave extraídas ou um código de idioma.

A saída é retornada como um único documento JSON, com resultados para cada documento de texto que você enviou, com base na ID. Posteriormente, você pode analisar, visualizar ou categorizar os resultados em insights acionáveis.

Os dados não são armazenados na sua conta. Operações executadas pela API de Análise de Texto não têm estado, o que significa que o texto que você fornece é processado e os resultados são retornados imediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Análise de Texto para vários níveis de experiência com programação

Você pode começar a usar a API de Análise de Texto em seus processos mesmo que não tenha muita experiência com programação. Use estes tutoriais para saber como você pode usar a API para analisar texto de maneiras diferentes de acordo com seu nível de experiência. 

* Programação mínima necessária:
    * [Use a API de Análise de Texto e o MS Flow para identificar o sentimento dos comentários em um grupo do Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integre o Power BI com a API de Análise de Texto para analisar comentários dos clientes](tutorials/tutorial-power-bi-key-phrases.md)
* Experiência de programação recomendada:
    * [Análise de sentimento em dados de streaming usando o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Crie um aplicativo Flask para traduzir texto, analisar o sentimento e sintetizar fala](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Idiomas com suporte

Esta seção foi movida para um artigo separado para melhor capacidade de descoberta. Consulte [Idiomas compatíveis na API de Análise de Texto](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos de extremidade da API de Análise de Texto aceitam dados de texto brutos. O limite atual é de 5.120 caracteres para cada documento; se precisar analisar documentos maiores, divida-os em partes menores.

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um único documento | 5\.120 caracteres, conforme medido por [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Tamanho máximo de toda a solicitação | 1 MB |
| Número máximo de documentos em uma solicitação | Mil documentos |

O limite de taxa varia de acordo com o tipo de preço.

| Camada          | Solicitações por segundo | Solicitações por minuto |
|---------------|---------------------|---------------------|
| S/Vários serviços | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

As solicitações são calculadas separadamente para cada recurso de Análise de Texto. Por exemplo, você pode enviar o número máximo de solicitações para o tipo de preço para cada recurso, ao mesmo tempo.      

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto usa codificação Unicode para representação de texto e cálculos de contagem de caracteres. As solicitações podem ser enviadas em UTF-8 e UTF-16 sem diferenças mensuráveis na contagem de caracteres. Pontos de código Unicode são usados como heurística para comprimento de caracteres e são considerados equivalentes para fins de limites de dados de análise de texto. Se você usar [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, você estará usando o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Próximas etapas

+ [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para Análise de Texto e obtenha uma chave e um ponto de extremidade para seus aplicativos.

+ Use o [início rápido](quickstarts/text-analytics-sdk.md) para começar a enviar chamadas à API. Saiba como enviar texto, escolher uma análise e exibir os resultados com o mínimo de código.

+ Veja [o que há de novo na API de Análise de Texto](whats-new.md) para obter informações sobre novas versões e recursos.

+ Se aprofunde um pouco mais com este [tutorial de análise de sentimento](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) usando o Azure Databricks.

+ Confira nossa lista de postagens em blog e outros vídeos demonstrando como usar a API de Análise de Texto com outras ferramentas e tecnologias em nossa [página Conteúdo da Comunidade e Externo](text-analytics-resource-external-community.md).
