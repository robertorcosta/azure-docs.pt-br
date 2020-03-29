---
title: Configurar o Personalizador
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219352"
---
# <a name="configure-personalizer-learning-loop"></a>Configurar o ciclo de aprendizado do Personalr

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.

Configure o ciclo de aprendizagem na página **Configuração,** no portal Azure para esse recurso Personalizador.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Configure recompensas para o loop de feedback

Configure o serviço para o uso de recompensas do seu ciclo de aprendizagem. As alterações nos valores a seguir redefinirão o modelo personalizador atual e o retreinarão com os últimos 2 dias de dados.

> [!div class="mx-imgBorder"]
> ![Configure os valores de recompensa para o loop de feedback](media/settings/configure-model-reward-settings.png)

|Valor|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizador coletará valores de recompensa para uma chamada de Classificação, a partir do momento em que ocorre a chamada de Classificação. Esse valor é definido perguntando: "Quanto tempo o Personalr deve esperar por chamadas de recompensas?" Qualquer recompensa recebida após essa janela será registrada em log, mas não será usada para o aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela de Tempo de Espera de Recompensa associada a uma Chamada de classificação, o Personalizador atribuirá a Recompensa Padrão. Por padrão, e na maioria dos cenários, a Recompensa Padrão é zero (0).|
|Agregação de recompensas|Se várias recompensas forem recebidas para a mesma chamada à API de Classificação, este método de agregação será usado: **soma** ou **mais anterior**. A opção "mais anterior" escolhe a pontuação mais anterior recebida e descarta o restante. Isso é útil se você quiser uma recompensa única entre as chamadas possivelmente duplicadas. |

Depois de alterar esses valores, certifique-se de selecionar **Salvar**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Configure a exploração para permitir que o ciclo de aprendizagem se adapte

A personalização é capaz de descobrir novos padrões e se adaptar às mudanças de comportamento do usuário ao longo do tempo, explorando alternativas em vez de usar a previsão do modelo treinado. O valor **de Exploração** determina qual porcentagem de chamadas rank são respondidas com exploração.

Alterações nesse valor redefinirão o modelo personalizador atual e o retreinarão com os últimos 2 dias de dados.

![O valor de exploração determina qual porcentagem de chamadas rank são respondidas com exploração](media/settings/configure-exploration-setting.png)

Depois de alterar esse valor, certifique-se de selecionar **Salvar**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Configurar freqüência de atualização de modelo para treinamento de modelos

A **freqüência de atualização do Modelo** define com que frequência o modelo é treinado.

|Configuração de freqüência|Finalidade|
|--|--|
|1 minuto|As freqüências de atualização de um minuto são úteis **ao depurar** o código de um aplicativo usando o Personalr, fazer demonstrações ou testar interativamente aspectos de aprendizagem de máquina.|
|15 minutos|Frequências de atualização de alto modelo são úteis para situações em que você deseja **acompanhar de perto as mudanças** nos comportamentos do usuário. Os exemplos incluem sites executados em notícias em tempo real, conteúdo viral ou licitação de produtos ao vivo. Você pode usar uma frequência de 15 minutos nesses cenários. |
|1 hora|Na maioria dos casos de uso, uma frequência de atualização mais baixa é eficaz.|

![A frequência de atualização do modelo define a frequência com que um novo modelo do Personalizador é treinado novamente.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar esse valor, certifique-se de selecionar **Salvar**.

## <a name="data-retention"></a>Retenção de dados

O **Período de retenção de dados** define por quantos dias o Personalizador mantém os logs de dados. Os logs de dados passados são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usados para medir a eficácia do Personalizador e otimizar a Política de Aprendizado.

Depois de alterar esse valor, certifique-se de selecionar **Salvar**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Configurações que incluem a redefinição do modelo

As seguintes ações incluem um retreinamento imediato do modelo com os últimos 2 dias de dados.

* Recompensa
* Exploração

Para [limpar](how-to-manage-model.md) todos os seus dados, use a página **Modelo e configurações de aprendizado *** .

## <a name="next-steps"></a>Próximas etapas

[Saiba como gerenciar seu modelo](how-to-manage-model.md)
