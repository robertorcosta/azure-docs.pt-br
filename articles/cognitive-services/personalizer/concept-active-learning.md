---
title: Eventos ativos e inativos – personalizador
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429043"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Quando o aplicativo chama a API de classificação, você recebe a ação que o aplicativo deve mostrar no campo rewardActionId.  A partir desse momento, o personalizador estará esperando uma chamada de recompensa com o mesmo eventId. A pontuação de recompensa será usada para treinar o modelo que será usado para futuras chamadas de classificação. Se nenhuma chamada de recompensa for recebida para o eventId, um prêmio bits será aplicado. As recompensas padrão são estabelecidas no portal do Azure.

Em alguns casos, o aplicativo pode precisar chamar o diâmetro de ordem, até mesmo sabe se o resultado será usado ou disparado para o usuário. Isso pode acontecer em situações em que, por exemplo, a renderização de página de conteúdo promovido é substituída por uma campanha de marketing. Se o resultado da chamada de classificação nunca foi usado e o usuário nunca conseguiu vê-la, seria incorreto treiná-la com qualquer recompensa, zero ou de outra forma.
Normalmente, isso acontece quando:

* Talvez você esteja renderizando previamente alguma interface do usuário que o usuário pode ou não ver. 
* Seu aplicativo pode estar realizando uma personalização preditiva na qual as Chamadas de classificação são realizadas com menos contexto de tempo real e sua saída pode ou não ser usada pelo aplicativo. 

Nesses casos, a maneira correta de usar o personalizador é chamar rank solicitando que o evento fique _inativo_. O personalizador não esperará um prêmio para esse evento e também não aplicará uma recompensa padrão. Letr em sua lógica de negócios, se o aplicativo usar as informações da chamada de classificação, tudo o que você precisa fazer é _Ativar_ o evento. A partir do momento em que o evento está ativo, o personalizador esperará um prêmio para o evento ou aplicará um recompensa padrão se nenhuma chamada explícita for feita na API de recompensa.

## <a name="get-inactive-events"></a>Obter eventos inativos

Para desabilitar o treinamento de um evento, chame Rank com `learningEnabled = False`.

O aprendizado de um evento inativo será ativado implicitamente se você enviar uma recompensa para o eventId ou chamar a API `activate` para esse eventId.

## <a name="learning-settings"></a>Configurações de aprendizado

As configurações de aprendizado determinam os *hiperparâmetros* específicos do treinamento do modelo. Dois modelos dos mesmos dados, treinados em diferentes configurações de aprendizado, acabarão sendo diferentes.

### <a name="import-and-export-learning-policies"></a>Importar e exportar políticas de aprendizado

É possível importar e exportar arquivos de política de aprendizado do portal do Azure. Isso permite que você salve políticas existentes, teste-as, substitua-as e arquive-as em seu controle do código-fonte como artefatos para referência e auditoria futuras.

### <a name="learning-policy-settings"></a>Configurações da política de aprendizado

As configurações na **Política de Aprendizado** não se destinam a serem alteradas. Só altere as configurações quando entender como elas afetam o Personalizador. Alterar as configurações sem esse conhecimento causará efeitos colaterais, incluindo a invalidação de modelos do Personalizador.

### <a name="comparing-effectiveness-of-learning-policies"></a>Como comparar a eficácia de políticas de aprendizado

É possível comparar como seria o desempenho de diferentes Políticas de Aprendizado com relação a dados passados em logs do Personalizador realizando [avaliações offline](concepts-offline-evaluation.md).

[Carregue suas próprias Políticas de Aprendizado](how-to-offline-evaluation.md) para comparar com a política de aprendizado atual.

### <a name="discovery-of-optimized-learning-policies"></a>Descoberta de políticas de aprendizado otimizadas

O Personalizador pode criar uma política de aprendizado mais otimizada ao fazer uma [avaliação offline](how-to-offline-evaluation.md). Uma política de aprendizado mais otimizada, que é mostrada para ter melhores recompensas em uma avaliação offline, renderá melhores resultados quando usada online no Personalizador.

Depois que uma política de aprendizado otimizada tiver sido criada, será possível aplicá-la diretamente ao Personalizador para que ela substitua a política atual imediatamente ou salvá-la para uma avaliação adicional e decidir, no futuro, se você deseja descartá-la, salvá-la ou aplicá-la posteriormente.
