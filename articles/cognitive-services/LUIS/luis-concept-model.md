---
title: Design com modelos - LUIS
titleSuffix: Azure Cognitive Services
description: A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de mais de uma maneira.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219990"
---
# <a name="design-with-intent-and-entity-models"></a>Design com intenção e modelos de entidade 

A compreensão da linguagem fornece vários tipos de modelos. Alguns modelos podem ser usados de mais de uma maneira. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 Autoral usa ensino de máquina

Luis permite que as pessoas ensinem facilmente conceitos a uma máquina. A máquina pode então construir modelos (aproximações funcionais de conceitos como classificadores e extratores) que podem ser usados para alimentar aplicações inteligentes. Embora o LUIS seja alimentado pelo aprendizado de máquina, a compreensão do aprendizado de máquina não é necessária para usá-lo. Em vez disso, os professores de máquinas comunicam conceitos ao LUIS mostrando exemplos positivos e negativos do conceito e explicando como um conceito deve ser modelado usando outros conceitos relacionados. Os professores também podem melhorar o modelo de LUIS de forma interativa, identificando e corrigindo os erros de previsão. 

## <a name="v3-authoring-model-decomposition"></a>Decomposição do modelo de autoria V3

Luis suporta _a decomposição do modelo_ com as APIs de autoria v3, dividindo o modelo em partes menores. Isso permite que você construa seus modelos com confiança em como as várias peças são construídas e previstas.

A decomposição do modelo tem as seguintes partes:

* [intenções](#intents-classify-utterances)
    * [descritores fornecidos](#descriptors-are-features) por recursos
* [entidades aprendidas por máquinas](#machine-learned-entities)
    * [subcomponentes](#entity-subcomponents-help-extract-data) (também entidades aprendidas por máquina)
        * [descritores fornecidos](#descriptors-are-features) por recursos 
        * [restrições fornecidas](#constraints-are-text-rules) por entidades não-aprendidas por máquinas, como expressões regulares e listas

## <a name="v2-authoring-models"></a>V2 Modelos de Autoria

A LUIS suporta entidades compostas com as APIs de autoria V2. Isso proporciona decomposição de modelo semelhante, mas não é o mesmo que a decomposição do modelo V3. A arquitetura de modelo recomendada é mover-se para a decomposição do modelo nas APIs de autoria v3. 

## <a name="intents-classify-utterances"></a>Intenções classificam expressões

Uma intenção classifica as declarações de exemplo para ensinar LUIS sobre a intenção. Enunciados de exemplo dentro de uma intenção são usados como exemplos positivos da expressão. Essas mesmas expressões são usadas como exemplos negativos em todas as outras intenções.

Considere um aplicativo que precisa determinar a intenção do usuário de encomendar um livro e um aplicativo que precise do endereço de envio para o cliente. Este aplicativo tem duas `OrderBook` `ShippingLocation`intenções: e .

A seguinte expressão **positive example** é um `OrderBook` exemplo positivo para a `ShippingLocation` `None` intenção e um **exemplo negativo** para as intenções: 

`Buy the top-rated book on bot architecture.`

O resultado de intenções bem projetadas, com suas expressões de exemplo, é uma previsão de alta intenção. 

## <a name="entities-extract-data"></a>Entidades extraem dados

Uma entidade representa uma unidade de dados que você deseja extraídos do enunciado. 

### <a name="machine-learned-entities"></a>Entidades aprendidas por máquinas

Uma entidade aprendida por máquina é uma entidade de alto nível que contém subcomponentes, que também são entidades aprendidas por máquina. 

**Use uma entidade aprendida por máquina:**

* quando os subcomponentes são necessários pelo aplicativo cliente
* para ajudar o algoritmo de aprendizagem de máquina a decompor entidades

Cada subcomponente pode ter:

* Subcomponentes
* restrições (entidade de expressão regular ou entidade de lista)
* descritores (características como uma lista de frases) 

Um exemplo de entidade aprendida por máquina é uma ordem para uma passagem de avião. Conceitualmente esta é uma transação única com muitas unidades menores de dados, como data, hora, quantidade de assentos, tipo de assento, como primeira classe ou ônibus, localização de origem, localização de destino e escolha da refeição.


### <a name="entity-subcomponents-help-extract-data"></a>Subcomponentes de entidades ajudam a extrair dados

Um subcomponente é uma entidade infantil aprendida por máquina dentro de uma entidade-mãe aprendida por máquina. 

**Use o subcomponente para:**

* compõem as partes da entidade aprendida por máquina (entidade-mãe).

O seguinte representa uma entidade aprendida por máquina com todos esses dados separados:

* TravelOrder (entidade aprendida por máquina)
    * DateTime (data pré-construídaV2)
    * Localização (entidade aprendida por máquina)
        * Origem (função encontrada através `from`do contexto como )
        * Destino (função encontrada através `to`do contexto como )
    * Assentos (entidade aprendida por máquina)
        * Quantidade (número pré-construído)
        * Qualidade (entidade aprendida por máquina com descritor de lista de frases)
    * Refeições (entidade aprendida por máquina com restrição de entidade de lista como escolhas alimentares)

Alguns desses dados, como a localização de origem e a localização do destino, devem `from` ser `to`aprendidos a partir do contexto do enunciado, talvez com palavras como e . Outras partes dos dados podem ser`Vegan`extraídas com correspondências exatas `Seattle` `Cairo`de cordas ( ) ou entidades pré-construídas (geografiaV2 de e ). 

Você projeta como os dados são combinados e extraídos por quais modelos você escolhe e como os configura.

### <a name="constraints-are-text-rules"></a>Restrições são regras de texto

Uma restrição é uma regra de correspondência de texto, fornecida por uma entidade não-aprendida por máquina, como a entidade de expressão regular ou uma entidade de lista. A restrição é aplicada no tempo de previsão para limitar a previsão e fornecer a resolução da entidade necessária pelo aplicativo do cliente. Você define essas regras ao escrever o subcomponente. 

**Use uma restrição:**
* quando você sabe o texto exato a extrair.

As restrições incluem:

* entidades [de expressão regulares](reference-entity-regular-expression.md)
* [listar](reference-entity-list.md) entidades 
* entidades [pré-construídas](luis-reference-prebuilt-entities.md)

Continuando com o exemplo da passagem de avião, os códigos do aeroporto podem estar em uma entidade de lista para correspondências de texto exatas. 

Para uma lista de aeroportos, a entrada `Seattle` da lista para Seattle é o nome da cidade, e os sinônimos para Seattle incluem o código do aeroporto para Seattle, juntamente com cidades e cidades vizinhas:

|`Seattle`Listar sinônimos de entidade|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Se você quiser reconhecer apenas 3 códigos de letras para códigos aeroportuários, use uma expressão regular como restrição. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Intenções versus entidades

Uma intenção é o resultado desejado de _todo_ o enunciado, enquanto as entidades são pedaços de dados extraídos do enunciado. Geralmente as intenções estão vinculadas às ações que o aplicativo cliente deve tomar e as entidades são informações necessárias para realizar essa ação. Do ponto de vista da programação, uma intenção desencadearia uma chamada de método e as entidades seriam usadas como parâmetros para essa chamada de método.

Esta enunciada _deve_ ter uma intenção e _pode_ ter entidades:

`Buy an airline ticket from Seattle to Cairo`

Esta declaração tem uma única intenção:

* Comprando uma passagem de avião

Este enunciado _pode_ ter várias entidades:

* Localizações de Seattle (origem) e Cairo (destino)
* A quantidade de um bilhete único

## <a name="descriptors-are-features"></a>Descritores são características

Um descritor é um recurso aplicado a um modelo na hora do treinamento, incluindo listas de frases e entidades. 

**Use um descritor quando quiser:**

* aumentar o significado de palavras e frases identificadas pelo descritor
* ter LUIS recomendar novo texto ou frases para recomendar para o descritor
* corrigir um erro sobre os dados de treinamento

## <a name="next-steps"></a>Próximas etapas

* Entenda [intenções](luis-concept-intent.md) e [entidades.](luis-concept-entity-types.md) 