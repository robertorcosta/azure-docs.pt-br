---
title: Características - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione recursos a um modelo de linguagem para fornecer dicas sobre como reconhecer a entrada que você deseja identificar ou classificar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531891"
---
# <a name="machine-learned-features"></a>Recursos aprendidos por máquina

No aprendizado de máquina, um _recurso_ é um traço distinto ou atributo de dados que seu sistema observa & aprende. No Language Understanding (LUIS), um recurso descreve e explica o que é significativo sobre suas intenções e entidades.

No [portal DE pré-visualização LUIS,](https://preview.luis.ai)os recursos são _descritores_ porque são usados para _descrever_ a intenção ou entidade.

## <a name="features-_descriptors_-in-language-understanding"></a>Características _(descritores)_ na compreensão da linguagem

Os recursos, também conhecidos como descritores, descrevem pistas para ajudar a compreensão da linguagem a identificar as expressões de exemplo. Os recursos incluem:

* Lista de frases como um recurso para intenções ou entidades
* Entidades como características para intenções ou entidades

As características devem ser consideradas como uma parte necessária do seu esquema para a decomposição do modelo.

## <a name="what-is-a-phrase-list"></a>O que é uma lista de frases

Uma lista de frases é uma lista de palavras, frases, números ou outros caracteres que ajudam a identificar o conceito que você está tentando identificar. A lista é insensível a casos.

## <a name="when-to-use-a-phrase-list"></a>Quando usar uma lista de frases

Com uma lista de frases, LUIS considera o contexto e generaliza para identificar itens semelhantes, mas não uma correspondência de texto exata. Se você precisa do seu aplicativo LUIS para ser capaz de generalizar e identificar novos itens, use uma lista de frases.

Quando você quiser ser capaz de reconhecer novas instâncias, como um agendador de reuniões que deve reconhecer os nomes de novos contatos ou um aplicativo de inventário que deve reconhecer novos produtos, comece com uma entidade aprendida por máquina. Em seguida, crie uma lista de frases que ajude LUIS a encontrar palavras com significado semelhante. Esta lista de frases orienta luis a reconhecer exemplos adicionando significado adicional ao valor dessas palavras.

Listas de frase são como um vocabulário específico ao domínio que ajudam a melhorar a qualidade da compreensão de intenções e entidades.

## <a name="considerations-when-using-a-phrase-list"></a>Considerações ao usar uma lista de frases

Uma lista de frases é aplicada, por padrão, a todos os modelos do aplicativo. Isso funcionará para listas de frases que podem cruzar todas as intenções e entidades. Para descomposabilidade, você deve aplicar uma lista de frases apenas aos modelos a que é relevante.

Se você criar uma lista de frases (criada globalmente por padrão), depois aplicá-la como descritor (recurso) a um modelo específico, ela será removida dos outros modelos. Essa remoção adiciona relevância à lista de frases para o modelo a que é aplicada, ajudando a melhorar a precisão que proporciona no modelo.

A `enabledForAllModels` bandeira controla este escopo do modelo na API.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Como usar uma lista de frases

[Crie uma lista de frases](luis-how-to-add-features.md) quando sua intenção ou entidade tiver palavras ou frases importantes, como:

* termos do setor
* gírias
* abreviações
* linguagem específica da empresa
* o texto é de outro idioma, mas frequentemente usado em seu aplicativo
* palavras-chave e frases em seus enunciados de exemplo

**Não** adicione todas as palavras ou frases possíveis. Em vez disso, adicione algumas palavras ou frases de cada vez, depois retreine e publique. À medida que a lista cresce com o tempo, você pode encontrar alguns termos têm muitas formas (sinônimos). Quebre isso em outra lista.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Quando usar uma entidade como recurso

Uma entidade pode ser adicionada como um recurso no nível de intenção ou entidade.

### <a name="entity-as-a-feature-to-an-intent"></a>Entidade como uma característica para uma intenção

Adicione uma entidade como descritor (recurso) a uma intenção quando a detecção dessa entidade for significativa para a intenção.

Por exemplo, se a intenção é reservar um voo e a entidade for informações sobre bilhetes (como o número de assentos, origem e destino), então encontrar a entidade de informações sobre bilhetes deve adicionar peso à previsão da intenção de voo do livro.

### <a name="entity-as-a-feature-to-another-entity"></a>Entidade como recurso para outra entidade

Uma entidade (A) deve ser adicionada como recurso a outra entidade (B) quando a detecção dessa entidade (A) for significativa para a previsão da entidade (B).

Por exemplo, se a entidade de endereço de rua (A) for detectada, então encontrar o endereço de rua (A) adiciona peso à previsão para a entidade de endereço de transporte (B).

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* [Amplie](schema-change-prediction-runtime.md) seus modelos de aplicativos em tempo de execução de previsão
* Veja [Adicionar recursos](luis-how-to-add-features.md) para saber como adicionar recursos ao seu aplicativo LUIS.
