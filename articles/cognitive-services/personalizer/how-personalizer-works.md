---
title: Como funciona o Personalizador – Personalizador
description: O _loop_ personalizador usa o Machine Learning para criar o modelo que prevê a ação principal para seu conteúdo. O modelo é treinado exclusivamente em seus dados que você enviou a ele com as chamadas de classificação e recompensa.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623768"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O recurso personalizador, seu _loop de aprendizado_, usa o Machine Learning para criar o modelo que prevê a ação principal para seu conteúdo. O modelo é treinado exclusivamente em seus dados que você enviou a ele com as chamadas de **classificação** e **recompensa** . Cada loop é completamente independente um do outro.

## <a name="rank-and-reward-apis-impact-the-model"></a>As APIs de classificação e recompensa afetam o modelo

Você envia _ações com recursos_ e _recursos de contexto_ para a API de classificação. A API de **Classificação** decide usar:

* _Exploração_: o modelo atual para decidir a melhor ação com base nos dados anteriores.
* _Explorar_: selecione uma ação diferente em vez da ação superior. Você [configura esse percentual](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) para o recurso personalizado no portal do Azure.

Você determina a pontuação de recompensa e envia essa pontuação para a API de recompensa. A API de **Recompensa**:

* Coleta dados para treinar o modelo gravando os recursos e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base na configuração especificada na _política de aprendizado_.

## <a name="your-system-calling-personalizer"></a>Seu sistema chamando personalizador

A seguinte imagem mostra o fluxo de arquitetura das chamadas de Classificação e Recompensa:

![alt text](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. Você envia _ações com recursos_ e _recursos de contexto_ para a API de classificação.

    * O personalizador decide se você deve explorar o modelo atual ou explorar novas opções para o modelo.
    * O resultado de classificação é enviado ao EventHub.
1. A classificação superior é retornada ao seu sistema como _ID de ação de recompensa_.
    Seu sistema apresenta esse conteúdo e determina uma pontuação de recompensa com base em suas próprias regras de negócio.
1. Seu sistema retorna a pontuação de recompensa para o loop de aprendizado.
    * Quando Personalizador receber a recompensa, a recompensa será enviada ao EventHub.
    * A classificação e a recompensa são correlacionadas.
    * O modelo de IA é atualizado de acordo com os resultados de correlação.
    * O mecanismo de inferência é atualizado com o novo modelo.

## <a name="personalizer-retrains-your-model"></a>O personalizador retreina seu modelo

O personalizador retreina seu modelo com base em sua configuração de **atualização de frequência de modelo** no recurso personalizado na portal do Azure.

O personalizador usa todos os dados atualmente retidos, com base na configuração de **retenção de dados** , em número de dias no recurso personalizado no portal do Azure.

## <a name="research-behind-personalizer"></a>A pesquisa por trás do Personalizador

O Personalizador se baseia em ciência e pesquisa de ponta na área de [Aprendizado de Reforço](concepts-reinforcement-learning.md), incluindo artigos, atividades de pesquisa e áreas contínuas de exploração do Microsoft Research.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [principais cenários](where-can-you-use-personalizer.md) do personalizador