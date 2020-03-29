---
title: Gerenciar configurações de modelo e aprendizado - Personalizador
description: O modelo aprendido por máquina e as configurações de aprendizado podem ser exportados para backup em seu próprio sistema de controle de origem.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624292"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Como gerenciar configurações de modelo e aprendizado

O modelo aprendido por máquina e as configurações de aprendizado podem ser exportados para backup em seu próprio sistema de controle de origem.

## <a name="export-the-personalizer-model"></a>Exportar o modelo do Personalizador

A partir da seção de gerenciamento de recursos para **configurações de modelo e aprendizado,** a criação do modelo de revisão e a última data atualizada e exporta o modelo atual. É possível usar o portal do Azure ou as APIs do Personalizador para exportar um arquivo de modelo para fins de arquivamento.

![Exportar o modelo atual do Personalizador](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Dados claros para o seu ciclo de aprendizado

1. No portal Azure, para o recurso Personalizador, na página **Modelo e configurações de aprendizado,** selecione **Limpar dados**.
1. Para limpar todos os dados e redefinir o ciclo de aprendizado para o estado original, selecione todas as 3 caixas de seleção.

    ![No portal Azure, dados claros do recurso Personalr.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Finalidade|
    |--|--|
    |Dados de personalização e recompensa registrados.|Esses dados de registro são usados em avaliações offline. Limpe os dados se estiver redefinindo seu recurso.|
    |Redefinir o modelo Personalizador.|Este modelo muda em cada retreinamento. Esta freqüência de treinamento é especificada na **freqüência do modelo de upload** na página **Configuração.** |
    |Defina a política de aprendizagem como padrão.|Se você mudou a política de aprendizagem como parte de uma avaliação offline, isso reinicia para a política de aprendizagem original.|

1. Selecione **Limpar dados selecionados** para iniciar o processo de compensação. O status é relatado nas notificações do Azure, na navegação superior direita.

## <a name="import-a-new-learning-policy"></a>Importe uma nova política de aprendizagem

As configurações da política de [aprendizagem](concept-active-learning.md#understand-learning-policy-settings) determinam os _hiperparâmetros_ do treinamento modelo. Realize uma [avaliação offline](how-to-offline-evaluation.md) para encontrar uma nova política de aprendizagem.

1. Abra o [portal Azure](https://portal.azure.com)e selecione seu recurso Personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção Gerenciamento de **recursos.**
1. Para as **configurações de aprendizagem Importar,** selecione o arquivo criado com o formato JSON especificado acima e selecione o botão **Upload.**

    Aguarde a notificação de que a política de aprendizagem foi enviada com sucesso.

## <a name="export-a-learning-policy"></a>Exportar uma política de aprendizagem

1. Abra o [portal Azure](https://portal.azure.com)e selecione seu recurso Personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção Gerenciamento de **recursos.**
1. Para as **configurações de aprendizagem Importar,** selecione o botão **Exportar configurações de aprendizagem.** Isso salva `json` o arquivo para o seu computador local.

## <a name="next-steps"></a>Próximas etapas

[Aprenda a gerenciar uma política de aprendizagem](how-to-manage-model.md)
