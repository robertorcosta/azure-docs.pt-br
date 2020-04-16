---
title: Diretrizes de nomeação de palavras-chave - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Criar uma palavra-chave eficaz é vital para garantir que seu dispositivo responda de forma consistente e precisa.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399839"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Diretrizes para criar uma palavra-chave eficaz

Criar uma palavra-chave eficaz é vital para garantir que seu dispositivo responda de forma consistente e precisa. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua marca. Neste artigo, você aprende alguns princípios orientadores para criar uma palavra-chave eficaz.

## <a name="choose-an-effective-keyword"></a>Escolha uma palavra-chave eficaz

Considere as seguintes diretrizes ao escolher uma palavra-chave:

> [!div class="checklist"]
> * Sua palavra-chave deve ser uma palavra ou frase em inglês.
> * Não deve levar mais dois segundos para ser dita.
> * Palavras com quatro a sete sílabas funcionam melhor. Por exemplo, "Ei, Computador" é uma boa palavra-chave. Apenas "Hey" ("Ei") seria uma ruim.
> * As palavras-chave devem seguir as regras comuns de pronúncia em inglês.
> * Uma palavra única ou até mesmo inventada que siga as regras comuns de pronúncia do português pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.
> * Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas costumam usar em conversas. Elas podem ser gatilhos falsos para o seu dispositivo.
> * Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os usuários precisariam saber a pronúncia "certa" para fazer o dispositivo responder. Por exemplo, “509” poderia ser pronunciado como "five zero nine" ("cinco zero nove") ou "five hundred and nine" ("quinhentos e nove"). "R.E.I." pode ser pronunciado como “r-e-i” ou “ray”. "Live" pode ser pronunciado "/līv/" ou "/liv/".
> * Não use caracteres especiais, símbolos nem dígitos. Por exemplo, "Go#" e "20 + cats" podem ser palavras-chave problemáticas. No entanto, "go sharp" ("fique esperto") ou "twenty plus cats" ("mais de vinte gatos") pode funcionar. Você ainda pode usar os símbolos na sua identidade visual, bem como marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra registrada como sua palavra-chave, certifique-se de possuir essa marca registrada ou que você tem permissão do proprietário da marca para usar a palavra. A Microsoft não é responsável por quaisquer problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [criar uma palavra-chave personalizada usando o Speech Studio](speech-devices-sdk-create-kws.md).
