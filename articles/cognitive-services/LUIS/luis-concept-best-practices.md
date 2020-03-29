---
title: Melhores práticas para construir seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: Aprenda as melhores práticas para obter os melhores resultados com o modelo do seu aplicativo LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280919"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Melhores práticas para construir um aplicativo de compreensão linguística (LUIS)
Use o processo de autoria do aplicativo para construir seu aplicativo LUIS: 

* Construir modelos de linguagem (intenções e entidades)
* Adicione algumas expressões de exemplo de treinamento (15-30 por intenção)
* Publicar para endpoint
* Testar do ponto de extremidade 

Uma vez que seu aplicativo seja [publicado,](luis-how-to-publish-app.md)use o ciclo de vida do desenvolvimento para adicionar recursos, publicar e testar a partir do ponto final. Não comece o próximo ciclo de autoria adicionando mais enunciados de exemplo, pois isso não permite que luis aprenda seu modelo com declarações de usuários do mundo real. 

Não expanda as expressões até que o conjunto atual de expressões de exemplo e ponto final estejam retornando confiantes e altos escores de previsão. Melhore as pontuações usando [o aprendizado ativo](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>O que fazer e o que não fazer
A lista a seguir inclui melhores práticas para aplicativos LUIS:

|O que fazer|O que não fazer|
|--|--|
|[Definir intenções distintas](#do-define-distinct-intents)<br>[Adicionar descritores a intenções](#do-add-descriptors-to-intents) |[Adicionar muitos exemplos de declaração a intenções](#dont-add-many-example-utterances-to-intents)<br>[Use poucas ou simples entidades](#dont-use-few-or-simple-entities) |
|[Localizar um ponto ideal entre muito genérico e muito específico para cada intenção](#do-find-sweet-spot-for-intents)|[Usar o LUIS como uma plataforma de treinamento](#dont-use-luis-as-a-training-platform)|
|[Construa seu aplicativo iterativamente com versões](#do-build-your-app-iteratively-with-versions)<br>[Construir entidades para decomposição de modelos](#do-build-for-model-decomposition)|[Adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Adicionar padrões em iterações posteriores](#do-add-patterns-in-later-iterations)|[Misturar a definição de intenções e entidades](#dont-mix-the-definition-of-intents-and-entities)|
|[Equilibrar suas declarações em todas as intenções](#balance-your-utterances-across-all-intents) exceto a intenção Nenhum.<br>[Adicionar exemplos de enunciado à intenção None](#do-add-example-utterances-to-none-intent)|[Criar descritores com todos os valores possíveis](#dont-create-descriptors-with-all-the-possible-values)|
|[Aproveitar o recurso de sugestão para aprendizado ativo](#do-leverage-the-suggest-feature-for-active-learning)|[Adicionar muitos padrões](#dont-add-many-patterns)|
|[Monitore o desempenho do seu aplicativo com testes em lote](#do-monitor-the-performance-of-your-app)|[Treinar e publicar com cada declaração de exemplo única adicionada](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Defina intenções distintas
Verifique se o vocabulário para cada intenção é apenas para essa intenção e não está se sobrepondo a uma intenção diferente. Por exemplo, se desejar ter um aplicativo que manipula organizações de viagem como voos por companhia aérea e hotéis, será possível optar por tê-las como áreas de assunto, intenções separadas ou a mesma intenção com entidades para dados específicos dentro da declaração.

Se o vocabulário entre duas intenções for o mesmo, combine a intenção e use entidades. 

Considere os seguintes exemplos de declaração:

|Exemplo de enunciados|
|--|
|Reservar um voo|
|Reservar um hotel|

`Book a flight`e `Book a hotel` usar o mesmo `book a `vocabulário de . Esse formato é o mesmo, por isso deve ser `flight` `hotel` a mesma intenção com as diferentes palavras de e como entidades extraídas. 

## <a name="do-add-descriptors-to-intents"></a>Adicionar descritores a intenções

Os descritores ajudam a descrever recursos para uma intenção. Um descritor pode ser uma lista de palavras que são significativas para essa intenção ou uma entidade que é significativa para essa intenção. 

## <a name="do-find-sweet-spot-for-intents"></a>Localizar ponto ideal para intenções
Use dados de previsão do LUIS para determinar se suas intenções estiverem se sobrepondo. Intenções sobrepostas confundem o LUIS. O resultado é que a principal intenção de pontuação está muito perto de outra intenção. Como o LUIS não usa o mesmo caminho exato por meio dos dados para treinamento a cada vez, uma intenção sobreposta tem uma chance de ser a primeira ou a segunda no treinamento. Convém que a pontuação da declaração para cada intenção esteja mais distante para que esse flip/flop não aconteça. A boa distinção para intenções deve resultar na principal intenção esperada toda vez. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Construa seu aplicativo iterativamente com versões

Cada ciclo de criação deve estar dentro de uma nova [versão](luis-concept-version.md), clonada de uma versão existente. 

## <a name="do-build-for-model-decomposition"></a>Construir para decomposição de modelo

A decomposição do modelo tem um processo típico de:

* criar **intenção** com base nas intenções do usuário do cliente-app
* adicionar 15-30 enunciados de exemplo com base na entrada do usuário do mundo real
* rótulo conceito de dados de alto nível em enunciado exemplo
* quebrar o conceito de dados em subcomponentes
* adicionar descritores (recursos) a subcomponentes
* adicionar descritores (recursos) à intenção 

Uma vez que você criou as declarações de intenção e adicionou exemplos, o exemplo a seguir descreve a decomposição da entidade. 

Comece identificando conceitos completos de dados que você deseja extrair em uma expressão. Esta é sua entidade aprendida por máquina. Em seguida, decomponha a frase em suas partes. Isso inclui identificar subcomponentes (como entidades), juntamente com descritores e restrições. 

Por exemplo, se você quiser extrair um endereço, a `Address`entidade mais aprendida pela máquina pode ser chamada . Ao criar o endereço, identifique alguns de seus subcomponentes, como endereço de rua, cidade, estado e código postal. 

Continue decompondo esses elementos **restringindo** o código postal a uma expressão regular. Decomponha o endereço da rua em partes de um número de rua (usando um número pré-construído), um nome de rua e um tipo de rua. O tipo de rua pode ser descrito com uma lista **de descritores** como avenida, círculo, estrada e pista.

A API de autoria v3 permite a decomposição do modelo. 

## <a name="do-add-patterns-in-later-iterations"></a>Adicione padrões em iterações posteriores

Você deve entender como o aplicativo se comporta antes de adicionar [padrões,](luis-concept-patterns.md) porque os padrões são ponderados mais pesadamente do que as expressões de exemplo e distorcem a confiança. 

Depois de entender como seu aplicativo se comporta, adicione padrões à medida que eles se aplicam ao seu aplicativo. Você não precisa adicioná-los a cada [iteração](luis-concept-app-iteration.md). 

Não há mal nenhum em adicioná-los no início do design do modelo, mas é mais fácil ver como cada padrão muda o modelo depois que o modelo é testado com expressões. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Equilibrar suas declarações em todas as intenções

Para que o LUIS tenha previsões precisas, a quantidade de enunciados de exemplo em cada intenção (exceto para a intenção Nenhum), deve ser relativamente semelhante. 

Se você tiver uma intenção com 100 declarações de exemplo e uma intenção com 20 declarações de exemplo, a intenção de expressão 100 terá uma taxa mais alta de previsão.  

## <a name="do-add-example-utterances-to-none-intent"></a>Adicionar declarações de exemplo à intenção None

Essa intenção é a intenção de recuo, indicando tudo fora da sua aplicação. Adicione um exemplo de declaração à intenção None para todos os 10 exemplos de declarações no restante do seu aplicativo LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Aproveite o recurso de sugestão para aprendizado ativo

Use **Examinar declarações de ponto de extremidade** do [aprendizado ativo](luis-how-to-review-endpoint-utterances.md) regularmente, em vez de adicionar mais exemplos de declarações a intenções. Como o aplicativo está constantemente recebendo declarações de ponto de extremidade, essa lista está crescendo e mudando.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitore o desempenho do seu aplicativo

Monitore a precisão da previsão usando um [conjunto de teste em lote](luis-concept-batch-test.md). 

Mantenha um conjunto separado de expressões que não são usadas como [expressões de exemplo](luis-concept-utterance.md) ou declarações de ponto final. Continue melhorando o aplicativo para seu conjunto de testes. Adapte o conjunto de teste para refletir declarações de usuário real. Use este conjunto de teste para avaliar cada iteração ou versão do aplicativo. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Não adicione muitos exemplos de declaração a intenções

Depois que o aplicativo for publicado, adicione apenas expressões do aprendizado ativo no processo de ciclo de vida do desenvolvimento. Se as declarações forem semelhantes demais, adicione um padrão. 

## <a name="dont-use-few-or-simple-entities"></a>Não use poucas ou entidades simples

As entidades são construídas para extração e previsão de dados. É importante que cada intenção tenha entidades aprendidas por máquina que descrevam os dados na intenção. Isso ajuda o LUIS a prever a intenção, mesmo que seu aplicativo cliente não precise usar a entidade extraída. 

## <a name="dont-use-luis-as-a-training-platform"></a>Não use o LUIS como uma plataforma de treinamento

O LUIS é específico para o domínio de um modelo de linguagem. Ele não é destinado a trabalhar como uma plataforma de treinamento geral de idioma natural. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Não adicionar muitas declarações de exemplo do mesmo formato, ignorando os outros formatos

O LUIS espera variações nas declarações de uma intenção. As declarações podem variar e ter o mesmo significado geral. As variações podem incluir o comprimento da declaração, a escolha de palavras e o posicionamento de palavras. 

|Não use o mesmo formato|Use o formato variável|
|--|--|
|Comprar uma passagem para Seattle<br>Comprar uma passagem para Paris<br>Comprar uma passagem para Orlando|Comprar 1 passagem para Seattle<br>Reservar duas poltronas no voo para Paris na próxima segunda-feira<br>Eu gostaria de reservar três passagens para Orlando para a semana do saco cheio|

A segunda coluna usa diferentes verbos (comprar e reservar), diferentes quantidades (1, duas, 3) e diferentes organizações de palavras, mas todas têm a mesma intenção de comprar passagens de avião para viagem. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Não misture a definição de intenções e entidades

Criar uma intenção para qualquer ação que seu bot usará. Use entidades como parâmetros que possibilitam a ação. 

Para um bot que reservará voos de companhias aéreas, crie uma intenção **de BookFlight.** Não crie uma intenção para toda companhia aérea ou para todo destino. Use essas partes de dados como [entidades](luis-concept-entity-types.md) e marque-as nos exemplos de declarações. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Não crie descritores com todos os valores possíveis

Forneça alguns exemplos nas listas de [frases](luis-concept-feature.md) do descritor, mas não em cada palavra. O LUIS generaliza e leva em conta o contexto. 

## <a name="dont-add-many-patterns"></a>Não adicione muitos padrões

Não adicione [padrões](luis-concept-patterns.md) demais. O LUIS destina-se a aprender rapidamente com menos exemplos. Não sobrecarregue o sistema desnecessariamente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Não treine nem publique com toda declaração de exemplo única

Adicione 10 ou 15 declarações antes do treinamento e da publicação. Isso permite ver o impacto sobre a precisão da previsão. Adicionar uma única declaração pode não ter um impacto visível sobre a pontuação. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [planejar seu aplicativo](luis-how-plan-your-app.md) no aplicativo LUIS.
