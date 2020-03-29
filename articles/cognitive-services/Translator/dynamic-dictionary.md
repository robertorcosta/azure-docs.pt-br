---
title: Dicionário Dinâmico - API de Texto do Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar o recurso de dicionário dinâmico da API de texto de tradutor de serviços cognitivos do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446703"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico é seguro apenas para substanções compostas, como nomes próprios e nomes de produtos.

**Sintaxe:**

<mstrans:dictionary translation="tradução da frase">frase</mstrans:dicionário>

**Requisitos:**

* Os `From` `To` idiomas devem incluir inglês e outro idioma suportado. 
* Você deve `From` incluir o parâmetro na sua solicitação de tradução de API em vez de usar o recurso autodetectar. 

**Exemplo: en-de:**

Entrada de origem:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Saída do alvo:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Esse recurso funciona da mesma forma com e sem o modo HTML.

Use o recurso com moderação. Uma maneira melhor de personalizar a tradução é usando o Personal Translator. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)sobre o Personal Translator em .
