---
title: Como funciona o Personalizador – Personalizador
description: O _loop_ Personalr usa aprendizado de máquina para construir o modelo que prevê a ação superior para o seu conteúdo. O modelo é treinado exclusivamente em seus dados que você enviou para ele com as chamadas Rank and Reward.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623768"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O recurso Personalr, seu _ciclo de aprendizagem,_ usa aprendizado de máquina para construir o modelo que prevê a ação superior para o seu conteúdo. O modelo é treinado exclusivamente em seus dados que você enviou para ele com as chamadas **Rank** and **Reward.** Cada loop é completamente independente um do outro.

## <a name="rank-and-reward-apis-impact-the-model"></a>APIs de classificação e recompensa impactam o modelo

Você envia _ações com recursos_ e recursos de _contexto_ para a API Rank. A API de **Classificação** decide usar:

* _Explorar_: O modelo atual para decidir a melhor ação com base em dados passados.
* _Explorar_: Selecione uma ação diferente em vez da ação superior. Você [configura essa porcentagem](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) para o seu recurso Personalizador no portal Azure.

Você determina a pontuação de recompensa e envia essa pontuação para a API de recompensa. A API de **Recompensa**:

* Coleta dados para treinar o modelo gravando os recursos e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base na configuração especificada na _Política de Aprendizagem_.

## <a name="your-system-calling-personalizer"></a>Seu sistema chamando Personalizador

A seguinte imagem mostra o fluxo de arquitetura das chamadas de Classificação e Recompensa:

![alt texto](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. Você envia _ações com recursos_ e recursos de _contexto_ para a API Rank.

    * O personalizador decide se deve explorar o modelo atual ou explorar novas opções para o modelo.
    * O resultado de classificação é enviado ao EventHub.
1. O primeiro lugar é devolvido ao seu sistema como _ID de ação de recompensa_.
    Seu sistema apresenta esse conteúdo e determina uma pontuação de recompensa com base em suas próprias regras de negócios.
1. Seu sistema retorna a pontuação de recompensa para o ciclo de aprendizado.
    * Quando Personalizador receber a recompensa, a recompensa será enviada ao EventHub.
    * A classificação e a recompensa são correlacionadas.
    * O modelo de IA é atualizado de acordo com os resultados de correlação.
    * O mecanismo de inferência é atualizado com o novo modelo.

## <a name="personalizer-retrains-your-model"></a>Personalizador retreina seu modelo

O personalizador retreina seu modelo com base na configuração **de atualização de freqüência do modelo** no recurso Personalizador no portal Azure.

O Personalizador usa todos os dados atualmente retidos, com base na configuração de **retenção** de dados em número de dias no recurso Personalizador no portal Azure.

## <a name="research-behind-personalizer"></a>A pesquisa por trás do Personalizador

O Personalizador se baseia em ciência e pesquisa de ponta na área de [Aprendizado de Reforço](concepts-reinforcement-learning.md), incluindo artigos, atividades de pesquisa e áreas contínuas de exploração do Microsoft Research.

## <a name="next-steps"></a>Próximas etapas

Conheça [os principais cenários](where-can-you-use-personalizer.md) do Personalandr