---
title: Configurar o Personalizador
titleSuffix: Azure Cognitive Services
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833903"
---
# <a name="configure-personalizer"></a>Configurar o Personalizador

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.

## <a name="create-personalizer-resource"></a>Crie um recurso do Personalizador

Crie um recurso do Personalizador para cada loop de comentários.

1. Entre no [Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva você à página **Criar** do serviço do Personalizador.
1. Insira o nome do seu serviço, selecione uma assinatura, um local, um tipo de preço e um grupo de recursos.
1. Selecione a confirmação e, em seguida, **Criar**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Configurar o serviço no portal do Azure

1. Entre no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Encontre o recurso do Personalizador.
1. Na seção **Gerenciamento de recursos** , selecione **configuração**.

    Antes de sair do portal do Azure, copie uma de suas chaves de recurso da página de **Chaves**. Você precisará disso para usar o [SDK do Personalizador](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Configurar recompensa para o loop de comentários com base no caso de uso

Configure o serviço para o uso de recompensas do seu loop de comentários. As alterações nos seguintes valores redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados:

![Configurar os valores de recompensa para o loop de comentários](media/settings/configure-model-reward-settings.png)

|Valor|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período de tempo durante o qual Personalizador coletará valores de recompensa para uma chamada de Classificação, a partir do momento em que ocorre a chamada de Classificação. Esse valor é definido perguntando: "quanto tempo o personalizador deve esperar por chamadas de recompensas?" Qualquer recompensa recebida após essa janela será registrada em log, mas não será usada para o aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo Personalizador durante a janela de Tempo de Espera de Recompensa associada a uma Chamada de classificação, o Personalizador atribuirá a Recompensa Padrão. Por padrão e na maioria dos cenários, a Recompensa Padrão é zero.|
|Agregação de recompensas|Se várias recompensas forem recebidas para a mesma chamada à API de Classificação, este método de agregação será usado: **soma** ou **mais anterior**. A opção "mais anterior" escolhe a pontuação mais anterior recebida e descarta o restante. Isso é útil se você deseja obter uma recompensa exclusiva entre chamadas possivelmente duplicadas. |

Depois de alterar esses valores, certifique-se de selecionar **salvar**.

### <a name="configure-exploration"></a>Configurar a exploração

A personalização pode descobrir novos padrões e se adaptar às alterações no comportamento do usuário ao longo do tempo explorando alternativas. O valor de **exploração** determina qual porcentagem de chamadas de classificação são respondidas com a exploração.

As alterações nesse valor redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados.

![O valor de exploração determina qual porcentagem de chamadas de classificação são respondidas com a exploração](media/settings/configure-exploration-setting.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

### <a name="model-update-frequency"></a>Frequência de atualização do modelo

O modelo mais recente, treinado com base nas chamadas à API de Recomenda de cada evento ativo, não é usado automaticamente pela chamada de Classificação do Personalizador. A **Frequência de atualização do modelo** define a frequência com que o modelo usado pela Chamada de classificação é atualizado.

As altas frequências de atualização do modelo são úteis para situações em que você deseja acompanhar de perto as alterações em comportamentos do usuário. Os exemplos incluem sites executados em notícias em tempo real, conteúdo viral ou licitação de produtos ao vivo. Você pode usar uma frequência de 15 minutos nesses cenários. Na maioria dos casos de uso, uma frequência de atualização mais baixa é eficaz. Frequências de atualização de um minuto são úteis ao depurar o código de um aplicativo usando o Personalizador, executar demonstrações ou testar interativamente aspectos de aprendizado de máquina.

![A frequência de atualização do modelo define a frequência com que um novo modelo do Personalizador é treinado novamente.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

### <a name="data-retention"></a>Retenção de dados

O **Período de retenção de dados** define por quantos dias o Personalizador mantém os logs de dados. Os logs de dados passados são necessários para realizar [avaliações offline](concepts-offline-evaluation.md), que são usados para medir a eficácia do Personalizador e otimizar a Política de Aprendizado.

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

## <a name="export-the-personalizer-model"></a>Exportar o modelo do Personalizador

Na seção do gerenciamento de recursos para **configurações de modelo e aprendizado**, reveja criação de modelo e data da última atualização e exporte o modelo atual. É possível usar o portal do Azure ou as APIs do Personalizador para exportar um arquivo de modelo para fins de arquivamento.

![Exportar o modelo atual do Personalizador](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Limpar dados para seu loop de aprendizado

1. Na portal do Azure, para o recurso personalizador, na página **configurações de modelo e aprendizado** , selecione **limpar dados**.
1. Para limpar todos os dados e redefinir o loop de aprendizagem para o estado original, marque todas as 3 caixas de seleção.

    ![Em portal do Azure, desmarque dados do recurso Personalizar.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Finalidade|
    |--|--|
    |Personalização registrada e dados de recompensa.|Esses dados de log são usados em avaliações offline. Limpe os dados se você estiver redefinindo seu recurso.|
    |Redefina o modelo personalizador.|Esse modelo é alterado em cada novo treinamento. Essa frequência de treinamento é especificada na **frequência do modelo de carregamento** na página de **configuração** . |
    |Defina a política de aprendizado como padrão.|Se você alterou a política de aprendizado como parte de uma avaliação offline, isso redefinirá a política de aprendizado original.|

1. Selecione **limpar dados selecionados** para iniciar o processo de limpeza. O status é relatado nas notificações do Azure, na navegação superior direita.

## <a name="next-steps"></a>Próximos passos

[Saiba como gerenciar uma política de aprendizado](how-to-learning-policy.md)
