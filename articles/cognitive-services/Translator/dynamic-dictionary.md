---
title: Dicionário Dinâmico - API de Texto do Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar o recurso de dicionário dinâmico do API de Tradução de Texto de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75446703"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para substantivos compostos, como nomes e nomes de produtos adequados.

**Sintaxe:**

<msTrans: Dictionary translation = "conversão de frase" >frase</msTrans: dicionário>

**Requisitos:**

* Os `From` idiomas `To` e devem incluir o inglês e outro idioma com suporte. 
* Você deve incluir o `From` parâmetro em sua solicitação de tradução de API em vez de usar o recurso de detecção automática. 

**Exemplo: en-de:**

Entrada de origem:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Saída de destino:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Esse recurso funciona da mesma forma com e sem o modo HTML.

Use o recurso com moderação. Uma maneira melhor de personalizar a tradução é usando o tradutor personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o tradutor personalizado [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)em.
