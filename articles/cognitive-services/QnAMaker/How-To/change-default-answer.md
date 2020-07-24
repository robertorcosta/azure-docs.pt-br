---
title: Obter QnA Maker de resposta padrão
description: A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: d37e63d84be58e6ccd2f1e23a1344961d39ffa01
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054172"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso de QnA Maker

A resposta padrão para uma base de dados de conhecimento deve ser retornada quando uma resposta não for encontrada. Se você estiver usando um aplicativo cliente, como o [serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), ele também poderá ter uma resposta padrão separada, indicando que nenhuma resposta atende ao limite de pontuação.

## <a name="types-of-default-answer"></a>Tipos de resposta padrão

Há dois tipos de resposta padrão na sua base de dados de conhecimento. É importante entender como e quando cada um é retornado de uma consulta de previsão:


|Tipo de pergunta|Descrição da resposta|
|--|--|
|Resposta da KB quando nenhuma resposta for determinada|`No good match found in KB.`-Quando a [API GenerateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) não encontra nenhuma resposta correspondente à pergunta, a `DefaultAnswer` configuração do serviço de aplicativo é retornada. Todas as bases de conhecimento no mesmo QnA Maker recurso compartilham o mesmo texto de resposta padrão.<br>Você pode gerenciar a configuração na portal do Azure, por meio do serviço de aplicativo ou com as APIs REST para [obter](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) ou [Atualizar](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) a configuração.|
|Texto de instrução de aviso de acompanhamento|Ao usar um prompt de acompanhamento em um fluxo de conversa, talvez você não precise de uma resposta no par QnA porque deseja que o usuário selecione entre os prompts de acompanhamento. Nesse caso, defina texto específico definindo o texto de resposta padrão, que é retornado com cada Previsão para avisos de acompanhamento. O texto deve ser exibido como texto de instrução para a seleção de prompts de acompanhamento. Um exemplo para esse texto de resposta padrão é `Please select from the following choices` . Essa configuração é explicada nas próximas seções deste documento. Também pode definir como parte da definição da base de dados de conhecimento de `defaultAnswerUsedForExtraction` usando a [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

### <a name="client-application-integration"></a>Integração de aplicativos cliente

Para um aplicativo cliente, como um bot com o **serviço de bot do Azure**, você pode escolher entre os seguintes cenários comuns:

* Usar a configuração da base de dados de conhecimento
* Use texto diferente no aplicativo cliente para distinguir quando uma resposta é retornada, mas não atende ao limite de pontuação. Esse texto pode ser um texto estático armazenado no código ou pode ser armazenado na lista de configurações do aplicativo cliente.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Definir a resposta padrão do prompt de acompanhamento ao criar a base de dados de conhecimento

Quando você cria uma nova base de dados de conhecimento, o texto de resposta padrão é uma das configurações. Se você optar por não defini-lo durante o processo de criação, poderá alterá-lo posteriormente com o procedimento a seguir.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>A resposta padrão do prompt de acompanhamento de alteração no portal QnA Maker

A resposta padrão da base de dados de conhecimento é retornada quando nenhuma resposta é retornada do serviço de QnA Maker.

1. Entre no portal de [QnA Maker](https://www.qnamaker.ai/) e selecione sua base de dados de conhecimento na lista.
1. Selecione **configurações** na barra de navegação.
1. Altere o valor do **texto de resposta padrão** na seção **gerenciar base de dados de conhecimento** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Captura de tela do portal QnA Maker, página configurações, com a caixa de texto resposta padrão realçada.":::

1. Selecione **salvar e treinar** para salvar a alteração.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma base de dados de conhecimento](../How-to/manage-knowledge-bases.md)
