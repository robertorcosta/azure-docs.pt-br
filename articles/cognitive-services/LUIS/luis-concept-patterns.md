---
title: Padrões ajudam previsão - LUIS
titleSuffix: Azure Cognitive Services
description: Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 6c1b548de25369c162b4a08dfa20fce62c17f99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75890282"
---
# <a name="patterns-improve-prediction-accuracy"></a>Padrões aumentam a precisão da previsão
Os padrões são definidos para aumentar a precisão quando várias expressões são muito similares.  Um padrão permite que você alcance maior precisão para uma intenção sem fornecer muitos enunciados a mais. 

## <a name="patterns-solve-low-intent-confidence"></a>Os padrões resolvem baixa confiabilidade de intenção
Considere um aplicativo de recursos humanos que relata o organograma em relação a um funcionário. Dado o nome e a relação de um funcionário, LUIS retorna os funcionários envolvidos. Considere um funcionário, Tom, com uma gerente chamada Alice e uma equipe de subordinados chamados: Michael, Rebecca e Carl.

![Imagem do organograma](./media/luis-concept-patterns/org-chart.png)

|Declarações|Intenção prevista|Pontuação da intenção|
|--|--|--|
|Quem está subordinado a Tom?|GetOrgChart|.30|
|Quem é subordinado de Tom?|GetOrgChart|.30|

Se um aplicativo tiver entre 10 e 20 expressões com comprimentos diferentes de frase, ordem de palavras diferentes e diferentes palavras (sinônimos de "subordinadas", "gerente", "relatório"), LUIS pode retornar uma baixa pontuação de confiabilidade. Crie um padrão para ajudar luis a entender a importância da ordem da palavra. 

Os padrões resolvem as situações a seguir: 

* A pontuação de intenção é baixa
* A intenção correta não é a pontuação máxima, mas muito perto da pontuação máxima. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Os padrões não são uma garantia de intenção
Os padrões usam uma mistura de tecnologias de previsão. Configurar uma intenção para uma expressão de modelo em um padrão não é uma garantia da previsão da intenção, mas é um sinal forte. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Padrões não melhoram a detecção de entidades aprendidas por máquina

Um padrão é destinado principalmente para ajudar a previsão de intenções e papéis. O _padrão.qualquer_ entidade é usado para extrair entidades de forma livre. Embora os padrões usem entidades, um padrão não ajuda a detectar uma entidade aprendida por máquina.  

Não espere ver melhorias na previsão de entidade se você recolher várias declarações em um único padrão. Para entidades simples dispararem, você precisa adicionar declarações ou usar entidades de lista, caso não seja despedido seu padrão.

## <a name="patterns-use-entity-roles"></a>Os padrões usam funções de entidades
Se duas ou mais entidades em um padrão estiverem relacionadas contextualmente, os padrões usarão as [funções](luis-concept-roles.md) da entidade para extrair informações contextuais sobre as entidades.  

## <a name="prediction-scores-with-and-without-patterns"></a>Pontuação da previsão com e sem padrões
Dadas expressões de exemplo suficientes, LUIS poderá aumentar a confiabilidade da previsão sem os padrões. Os padrões de aumentam a pontuação de confiabilidade sem ter que fornecer tantas expressões.  

## <a name="pattern-matching"></a>Correspondência de padrões
Um padrão é correspondido com base na detecção das entidades dentro do padrão primeiro, em seguida, validando o restante das palavras e a ordem de palavras do padrão. As entidades são exigidas no padrão para que um padrão corresponda. O padrão é aplicado no nível de token, não no nível do caractere. 

## <a name="pattern-only-apps"></a>Aplicativos somente para padrões
Você pode construir um aplicativo com intenções que não tenham expressões de exemplo, desde que haja um padrão para cada intenção. Para um aplicativo somente padrão, o padrão não deve conter entidades aprendidas por máquina, porque elas requerem expressões de exemplo. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Sintaxe de padrões

Aprenda a sintaxe padrão a partir da [referência de sintaxe padrão](reference-pattern-syntax.md). 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre padrões:

* [Como adicionar padrões](luis-how-to-model-intent-pattern.md)
* [Como adicionar padrão.qualquer entidade](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Padrões De Sintaxe](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [Saiba como implementar padrões neste tutorial](luis-tutorial-pattern.md)
