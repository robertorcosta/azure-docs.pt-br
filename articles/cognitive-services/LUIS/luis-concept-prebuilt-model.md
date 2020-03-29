---
title: Modelos pré-construídos - LUIS
titleSuffix: Azure Cognitive Services
description: Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar o aplicativo com um domínio predefinido ou, posteriormente adicionar um domínio relevante ao aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280830"
---
# <a name="prebuilt-models"></a>Modelos predefinidos

Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar seu aplicativo com um modelo pré-construído ou adicionar um modelo relevante ao seu aplicativo mais tarde. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos predefinidos

Luis fornece três tipos de modelos pré-construídos. Cada modelo poderá ser adicionado ao aplicativo a qualquer momento. 

|Tipo de modelo|Includes|
|--|--|
|[Domínio](luis-reference-prebuilt-domains.md)|Intenções, enunciados, entidades|
|Intenções|Intenções, enunciados|
|[Entities](luis-reference-prebuilt-entities.md)|Apenas entidades| 

## <a name="prebuilt-domains"></a>Domínios predefinidos

O Entendimento de Linguagem (LUIS) fornece *domínios pré-construídos,* que são modelos pré-treinados de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que trabalham em conjunto para domínios ou categorias comuns de aplicativos clientes. 

Os domínios predefinidos são treinados e prontos para serem adicionados ao aplicativo LUIS. As intenções e entidades de um domínio pré-construído são totalmente personalizáveis depois de adicioná-las ao seu aplicativo. 

> [!TIP]
> As intenções e as entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar intenções e entidades do mesmo domínio, quando for possível.
> O domínio predefinido de Utilitários tem intenções que você pode personalizar para uso em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo para reconhecer qualquer ação que o usuário quiser repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinido

Você pode achar que um domínio predefinido contém uma intenção que é semelhante a uma que você deseja ter em seu aplicativo do LUIS mas quer que tenha um comportamento diferente. Por exemplo, o domínio Locais `MakeReservation` pré-construídos fornece a intenção de fazer uma reserva de restaurante, mas você quer que seu aplicativo use essa intenção de fazer reservas de hotéis. **Places** Nesse caso, você pode modificar o comportamento dessa intenção adicionando expressões de exemplo à intenção de fazer reservas de hotéis e, em seguida, retreinar o aplicativo. 

Você pode encontrar uma lista completa dos domínios predefinidos na [Referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intenções predefinidas

Luis fornece intenções pré-construídas e suas expressões para cada um de seus domínios pré-construídos. As intenções podem ser adicionadas sem adicionar todo o domínio. Adicionar uma intenção é o processo de adicionar uma intenção e suas declarações ao seu aplicativo. Tanto o nome da intenção como a lista de enunciados podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades predefinidas

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS. Para obter uma lista completa de entidades predefinidas compatíveis com LUIS, incluindo suporte por cultura, veja a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas em seu aplicativo publicado. O comportamento das entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **builtin.datetime** foi preterido. Ele foi substituído por [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece o reconhecimento de intervalos de data e hora, bem como reconhecimento aprimorado de datas e horas ambíguas.

## <a name="next-steps"></a>Próximas etapas

Saiba como [adicionar entidades predefinidas](luis-prebuilt-entities.md) ao aplicativo.
