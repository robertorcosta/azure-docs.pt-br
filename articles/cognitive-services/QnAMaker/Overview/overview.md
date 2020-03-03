---
title: O que é o serviço QnA Maker?
titleSuffix: Azure Cognitive Services
description: O QnA Maker é um serviço de NLP baseado em nuvem que cria facilmente uma camada de conversa natural sobre seus dados. Ele pode ser usado para encontrar a resposta mais apropriada para qualquer entrada de idioma natural proveniente de sua KB (base de dados de conhecimento) personalizada de informações.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 91a63e87e937eab4c02d2b86026aa09472cb15ef
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561079"
---
# <a name="what-is-the-qna-maker-service"></a>O que é o serviço QnA Maker?

O QnA Maker é um serviço de NLP (processamento de idioma natural) baseado em nuvem que cria facilmente uma camada de conversa natural sobre seus dados. Ele pode ser usado para encontrar a resposta mais apropriada para qualquer entrada de idioma natural proveniente de sua KB (base de dados de conhecimento) personalizada de informações.

Um aplicativo cliente para o QnA Maker é qualquer aplicativo que se comunica com um usuário em linguagem natural para responder a uma pergunta. Exemplos de aplicativos clientes incluem aplicativos de mídia social, chatbots e aplicativos para área de trabalho habilitados para fala.

## <a name="when-to-use-qna-maker"></a>Quando usar o QnA Maker

* **Quando você tem informações** estáticas – use o QnA Maker quando você tem informações estáticas na base de dados de conhecimento de respostas. Essa base de dados de conhecimento é personalizada para suas necessidades e você a criou com documentos como [PDFs e URLs](../concepts/content-types.md).
* **Quando você deseja fornecer a mesma resposta a uma solicitação, pergunta ou comando** – quando usuários diferentes enviam a mesma pergunta, a mesma resposta é retornada.
* **Quando você deseja filtrar informações estáticas com base em metainformações** – adicione marcas de [metadados](../how-to/metadata-generateanswer-usage.md) para fornecer opções de filtragem adicionais relevantes para as informações e os usuários do aplicativo cliente. Informações de metadados comuns incluem [bate-papo](../how-to/chit-chat-knowledge-base.md) e características do conteúdo, tais como formato, tipo, finalidade e atualização.
* **Quando você deseja gerenciar uma conversa de bot que inclui informações estáticas** – a base de dados de conhecimento usa um comando ou texto de conversa de um usuário e o responde. Se a resposta fizer parte de um fluxo de conversa predeterminado, representado na sua base de dados de conhecimento com o [contexto de vários turnos](../how-to/multiturn-conversation.md), o bot poderá facilmente fornecer esse fluxo.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Usar a base de dados de conhecimento do QnA Maker em um chatbot

Quando uma base de dados de conhecimento do QnA Maker é publicada, um aplicativo cliente envia uma pergunta para o ponto de extremidade dessa base de dados de conhecimento e recebe os resultados como uma resposta JSON. Um aplicativo cliente comum para o QnA Maker é um chatbot.

![Fazer uma pergunta a um bot e obter uma resposta do conteúdo da base de dados de conhecimento](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Etapa|Ação|
|:--|:--|
|1|O aplicativo cliente envia a _pergunta_ do usuário (texto nas palavras dele), "Como fazer para atualizar programaticamente minha base de dados de conhecimento?" ao ponto de extremidade da base de dados de conhecimento.|
|2|Para obter a melhor resposta, o QnA Maker usa a base de dados de conhecimento treinada para fornecer a resposta correta e quaisquer avisos de acompanhamento que possam ser usados para refinar a pesquisa. O QnA Maker retorna uma resposta formatada em JSON.|
|3|O aplicativo cliente usa a resposta JSON para tomar decisões sobre como continuar a conversa. Essas decisões podem incluir mostrar a resposta principal e apresentar mais opções para refinar a pesquisa a fim de obter a melhor resposta. |
|||

## <a name="what-is-a-knowledge-base"></a>O que é uma base de dados de conhecimento?

O QnA Maker [importa o conteúdo](../concepts/knowledge-base.md) para uma base de dados de conhecimento de conjuntos de perguntas e respostas. O processo de importação extrai informações sobre a relação entre as partes do conteúdo estruturado e semiestruturado para pressupor relações entre os conjuntos de perguntas e respostas. Você pode editar esses conjuntos de perguntas e respostas ou adicionar novos conjuntos.

O conteúdo do conjunto de perguntas e respostas inclui:
* Todas as formas alternativas da pergunta
* Marcas de metadados usadas para filtrar as opções de resposta durante a pesquisa
* Avisos de acompanhamento para continuar o refinamento da pesquisa

![Exemplo de pergunta e resposta com metadados](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Depois de publicar a base de dados de conhecimento, um aplicativo cliente envia uma pergunta do usuário para o ponto de extremidade. O serviço QnA Maker processa a pergunta e fornece a melhor resposta.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Criar, gerenciar e publicar em um bot sem usar código

O portal do QnA Maker fornece a experiência completa de criação da base de dados de conhecimento. Você pode importar documentos na forma atual deles para a base de dados de conhecimento. Esses documentos (como perguntas frequentes, manuais do produto, planilhas ou páginas da Web) são convertidos em conjuntos de perguntas e respostas. Cada conjunto é analisado para solicitações de acompanhamento e conectado a outros conjuntos. O formato de _Markdown_ final dá suporte a apresentação avançada, incluindo imagens e links.

Depois que a base de dados de conhecimento for editada, publique a base de dados de conhecimento em um [bot de aplicativo Web do Azure](https://azure.microsoft.com/services/bot-service/) em funcionamento sem escrever nenhum código. Teste o bot no [portal do Azure](https://portal.azure.com) ou baixe-o e continue o desenvolvimento.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>A qualidade e a classificação da pesquisa fornecem a melhor resposta possível

O sistema de QnA Maker é uma abordagem de classificação em camadas. Os dados são armazenados no Azure Search, que também serve como a primeira camada de classificação. Os principais resultados do Azure Search são passados pelo modelo de reclassificação de NLP do QnA Maker para produzir os resultados finais e a pontuação de confiança.

## <a name="qna-maker-improves-the-conversation-process"></a>O QnA Maker melhora o processo de conversa

O QnA Maker fornece avisos de vários turnos e aprendizado ativo para ajudá-lo a melhorar os conjuntos básicos de perguntas e respostas.

Os **avisos de vários turnos** oferecem a oportunidade de conectar pares de perguntas e respostas. Essa conexão permite que o aplicativo cliente forneça uma resposta principal e oferece mais perguntas para refinar a pesquisa para uma resposta final.

Depois que a base de dados de conhecimento receber perguntas de usuários no ponto de extremidade publicado, o QnA Maker aplicará o **aprendizado ativo** a essas perguntas do mundo real para sugerir alterações na base de dados de conhecimento a fim de melhorar a qualidade.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O QnA Maker fornece criação, treinamento e publicação, junto com as permissões de colaboração, para integrar no ciclo de vida de desenvolvimento completo.

> [!div class="mx-imgBorder"]
> ![Imagem conceitual do ciclo de desenvolvimento](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Como fazer para começar?

**Etapa 1**: crie um recurso do QnA Maker no [portal do Azure](https://portal.azure.com).

**Etapa 2**: crie uma base de dados de conhecimento no portal do [QnA Maker](https://www.qnamaker.ai). Adicione [arquivos e URLs](../concepts/content-types.md) para criar a base de dados de conhecimento.

**Etapa 3**: Publique a base de dados de conhecimento e teste do ponto de extremidade personalizado usando [cURL ou Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Etapa 4**: Em seu aplicativo cliente, chame programaticamente o ponto de extremidade da base de dados de conhecimento. O aplicativo cliente processa a resposta JSON para mostrar a melhor resposta ao usuário.

## <a name="next-steps"></a>Próximas etapas
O QnA Maker fornece tudo o que você precisa para criar, gerenciar e implantar a base de dados de conhecimento personalizada.

> [!div class="nextstepaction"]
> [Examinar as alterações mais recentes](../whats-new.md)
