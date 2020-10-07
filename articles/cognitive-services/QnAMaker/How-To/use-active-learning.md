---
title: Usar o aprendizado ativo com a base de dados de conhecimento-QnA Maker
description: Aprenda a melhorar a qualidade de sua base de dados de conhecimento com o aprendizado ativo. Examine, aceite ou rejeite, adicione sem remover ou altere as perguntas existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c67cde3062d88c48eb44a5135bf47ad3948cecc8
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776792"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usar o aprendizado ativo para melhorar sua base de dados de conhecimento

O [aprendizado ativo](../Concepts/active-learning-suggestions.md) permite que você aprimore a qualidade de sua base de dados de conhecimento sugerindo perguntas alternativas. Os envios de usuários são levados em consideração e aparecem como sugestões na lista de perguntas alternativas. Você tem a flexibilidade de adicionar essas sugestões como perguntas alternativas ou rejeitá-las.

Sua base de dados de conhecimento não é alterada automaticamente. Para que qualquer alteração entre em vigor, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Atualizar a versão de tempo de execução para usar o aprendizado ativo

O aprendizado ativo é compatível com a versão de runtime 4.4.0 e superior. Se sua base de dados de conhecimento foi criada em uma versão anterior, [atualize seu runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) para usar esse recurso.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Ativar o aprendizado ativo para perguntas alternativas

O aprendizado ativo está desativado por padrão. Ative-o para ver sugestões de perguntas. Depois de ativar o aprendizado ativo, você precisa enviar informações do aplicativo cliente para QnA Maker. Para obter mais informações, consulte [o fluxo arquitetônico para usar GenerateAnswer e treinar APIs de um bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selecione **publicar** para publicar a base de dados de conhecimento. Consultas de aprendizado ativo são coletadas somente do ponto de extremidade de previsão da API GenerateAnswer. As consultas ao painel de teste no portal de QnA Maker não afetam o aprendizado ativo.

1. Para ativar o aprendizado ativo no portal de QnA Maker, vá para o canto superior direito, selecione seu **nome**, vá para [**configurações de serviço**](https://www.qnamaker.ai/UserSettings).

    ![Ative as alternativas de perguntas sugeridas do aprendizado ativo na página Configurações de serviço. Selecione seu nome de usuário no menu superior direito e, em seguida, selecione configurações de serviço.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Localize o serviço QnA Maker e, em seguida, ative o **aprendizado ativo**.

    > [!div class="mx-imgBorder"]
    > [![Na página Configurações de serviço, alterne o recurso de aprendizado ativo. Se não for possível alternar o recurso, talvez seja necessário atualizar seu serviço.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > A versão exata na imagem anterior é mostrada apenas como um exemplo. Sua versão pode ser diferente.

    Quando o **aprendizado ativo** é habilitado, a base de dados de conhecimento sugere novas perguntas em intervalos regulares com base em perguntas enviadas pelo usuário. Você pode desabilitar o **aprendizado ativo** desativando a configuração novamente.

## <a name="review-suggested-alternate-questions"></a>Revisar as perguntas alternativas sugeridas

[Examine as perguntas sugeridas alternativas](improve-knowledge-base.md) na página de **edição** de cada base de dados de conhecimento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento](./manage-knowledge-bases.md)