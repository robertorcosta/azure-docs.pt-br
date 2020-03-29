---
title: Eventos ativos e inativos - Personalizador
description: Este artigo discute o uso de eventos ativos e inativos dentro do serviço Personalizador.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624248"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Um evento **ativo** é qualquer chamada para Rank onde você sabe que vai mostrar o resultado para o cliente e determinar a pontuação de recompensa. Esse é o comportamento padrão.

Um evento **inativo** é uma chamada para Rank onde você não tem certeza se o usuário algum dia verá a ação recomendada, devido à lógica do negócio. Isso permite que você descarte o evento para que o Personalr não seja treinado com a recompensa padrão. Eventos inativos não devem chamar a API de recompensa.

É importante que o ciclo de aprendizagem saiba o tipo real de evento. Um evento inativo não terá uma chamada de recompensa. Um evento ativo deve ter uma chamada de recompensa, mas se a chamada da API nunca for feita, a pontuação de recompensa padrão será aplicada. Mude o status de um evento de inativo para ativo assim que você souber que influenciará a experiência do usuário.

## <a name="typical-active-events-scenario"></a>Cenário típico de eventos ativos

Quando seu aplicativo chama a API Rank, você recebe a ação, que o aplicativo deve mostrar no campo **rewardActionId.**  A partir desse momento, o Personalando espera uma chamada de recompensa com uma pontuação de recompensa que tenha o mesmo eventId. A pontuação de recompensa é usada para treinar o modelo para futuras chamadas de Rank. Se nenhuma chamada de recompensa for recebida para o eventId, uma recompensa padrão será aplicada. [As recompensas padrão](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no recurso Personalr no portal Azure.

## <a name="other-event-type-scenarios"></a>Outros cenários do tipo de evento

Em alguns cenários, o aplicativo pode precisar chamar rank antes mesmo de saber se o resultado será usado ou exibido para o usuário. Isso pode acontecer em situações em que, por exemplo, a renderização de página de conteúdo promovido é substituída por uma campanha de marketing. Se o resultado da chamada Rank nunca foi usado e o usuário nunca a viu, não envie uma chamada de recompensa correspondente.

Normalmente, esses cenários acontecem quando:

* Você está prerendering UI que o usuário pode ou não ver.
* Seu aplicativo está fazendo personalização preditiva na qual as chamadas rank são feitas com pouco contexto em tempo real e o aplicativo pode ou não usar a saída.

Nestes casos, use o Personalizador para chamar rank, solicitando que o evento seja _inativo_. O personalista não espera uma recompensa por este evento, e não aplicará uma recompensa padrão.

Mais tarde, na sua lógica de negócios, se o aplicativo usar as informações da chamada Rank, basta _ativar_ o evento. Assim que o evento estiver ativo, o Personalr espera uma recompensa pelo evento. Se nenhuma chamada explícita for feita para a API de recompensa, o Personalr aplica uma recompensa padrão.

## <a name="inactive-events"></a>Eventos inativos

Para desativar o treinamento para um `learningEnabled = False`evento, ligue para rank usando .

Para um evento inativo, o aprendizado é ativado implicitamente se você `activate` enviar uma recompensa para o eventId ou chamar a API para esse eventId.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como determinar a pontuação da recompensa e quais dados considerar.](concept-rewards.md)
