---
title: Testar seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73486685"
---
# <a name="testing-example-utterances-in-luis"></a>Testando os enunciados de exemplo no LUIS

O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS. 

Você pode testar LUIS interativamente, uma expressão de cada vez, ou fornecer um de declarações. Durante os testes, você pode comparar a resposta de previsão do modelo ativo atual com a resposta de previsão do modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação no teste?
Confira os conceitos [Pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Teste interativo
O teste interativo é feito a partir do painel de **teste** do portal LUIS. É possível inserir uma declaração para ver como as intenções e entidades são identificadas e pontuadas. Se luis não está prevendo as intenções e entidades como você espera em uma declaração no painel de testes, copie-a para a página **Intenção** como um novo enunciado. Em seguida, rotular as partes desse enunciado para entidades, e treinar LUIS. 

## <a name="batch-testing"></a>Teste em lote
Confira o [teste de lote](luis-concept-batch-test.md) se estiver testando mais de uma declaração por vez.

## <a name="endpoint-testing"></a>Teste do ponto de extremidade
É possível testar usando o [ponto de extremidade](luis-glossary.md#endpoint) com no máximo duas versões do seu aplicativo. Com sua versão principal ou em tempo real do seu aplicativo definida como o ponto de extremidade de **produção**, adicione uma segunda versão ao ponto de extremidade de **preparo**. Essa abordagem oferece três versões de uma declaração: o modelo atual no painel de teste do site do [LUIS](luis-reference-regions.md) e as duas versões nos dois pontos de extremidade diferentes. 

Todo o teste de ponto de extremidade conta para sua cota de uso. 

## <a name="do-not-log-tests"></a>Não registrar testes
Se você fizer testes com relação a um ponto de extremidade e não quiser que a declaração fique registrada, lembre-se de usar a configuração da cadeia de caracteres de consulta `logging=false`.

## <a name="where-to-find-utterances"></a>Onde encontrar declarações
Luis armazena todas as declarações registradas no registro de consultas, disponíveis para download no portal LUIS a partir da página da lista **de aplicativos,** bem como nas [APIs de autoria do](https://go.microsoft.com/fwlink/?linkid=2092087)LUIS. 

Quaisquer declarações das quais o LUIS não tem certeza são listadas na página **[Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)** do site do [LUIS](luis-reference-regions.md). 

## <a name="remember-to-train"></a>Lembrar-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o LUIS após fazer alterações no modelo. As alterações no aplicativo LUIS não são vistas no teste até que o aplicativo seja treinado. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [testar](luis-interactive-test.md) suas declarações.
