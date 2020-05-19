---
title: Dicionário dinâmico-Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como usar o recurso de dicionário dinâmico do tradutor de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3ef881efc417a6c3816a92d9af7d77bba74355aa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592535"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Como usar um dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para substantivos compostos, como nomes e nomes de produtos adequados.

**Sintaxe:**

<msTrans: Dictionary translation = "conversão de frase" >frase</msTrans: dicionário>

**Requisitos:**

* Os `From` `To` idiomas e devem incluir o inglês e outro idioma com suporte. 
* Você deve incluir o `From` parâmetro em sua solicitação de tradução de API em vez de usar o recurso de detecção automática. 

**Exemplo: en-de:**

Entrada de origem:`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Saída de destino:`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Esse recurso funciona da mesma forma com e sem o modo HTML.

Use o recurso com moderação. Uma maneira melhor de personalizar a tradução é usando o tradutor personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o tradutor personalizado em [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .