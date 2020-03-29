---
title: Melhorar a base de dados de conhecimento – QnA Maker
description: Melhore a qualidade de sua base de conhecimento com aprendizado ativo. Revisar, aceitar ou rejeitar, adicionar sem remover ou alterar perguntas existentes.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071128"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usar o aprendizado ativo para melhorar sua base de dados de conhecimento

[O aprendizado ativo](../Concepts/active-learning-suggestions.md) permite que você melhore a qualidade de sua base de conhecimento, sugerindo perguntas alternativas. As submissões de usuários são levadas em consideração e aparecem como sugestões na lista de perguntas alternativas. Você tem a flexibilidade de adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

Sua base de dados de conhecimento não é alterada automaticamente. Para que qualquer mudança tenha efeito, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar a versão em tempo de execução para usar o aprendizado ativo

O aprendizado ativo é compatível com a versão de runtime 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para usar esse recurso.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ativar o aprendizado ativo para perguntas alternativas

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. Depois de ativar o aprendizado ativo, você precisa enviar informações do aplicativo cliente para o QnA Maker. Para obter mais informações, consulte [Fluxo arquitetônico para usar ASIs de GerarResposta e Treinar a partir de um bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **Publicar** para publicar a base de conhecimento. As consultas de aprendizagem ativas são coletadas apenas do ponto final de previsão da API GenerateAnswer. As consultas ao painel Teste no portal QnA Maker não afetam o aprendizado ativo.

1. Para ativar o aprendizado ativo no portal QnA Maker, vá para o canto superior direito, selecione seu **nome,** vá para [**Configurações de serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ativar as alternativas de perguntas sugeridas do aprendizado ativo a partir da página configurações do Serviço. Selecione o nome do usuário no menu superior direito e selecione Configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**.

    > [!div class="mx-imgBorder"]
    > [![Na página configurações do Serviço, alterne o recurso Aprendizagem Ativa. Se você não for capaz de alternar o recurso, talvez seja necessário atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. Sua versão pode ser diferente.

    Uma vez habilitado **o Active Learning,** a base de conhecimento sugere novas perguntas em intervalos regulares com base em perguntas enviadas pelo usuário. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

## <a name="review-suggested-alternate-questions"></a>Revisão sugeriu perguntas alternativas

[Revisar perguntas alternativas sugeridas](improve-knowledge-base.md) na página **Editar** de cada base de conhecimento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crie uma base de conhecimento](./manage-knowledge-bases.md)