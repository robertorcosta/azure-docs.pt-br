---
title: Pontuação de confiança – QnA Maker
titleSuffix: Azure Cognitive Services
description: Essa pontuação de confiança indica que a resposta é a correspondência ideal da consulta do usuário.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229112"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Pontuação de confiança de uma base de dados de conhecimento do QnA Maker
Quando uma consulta de usuário é comparada com uma base de conhecimento, o QnA Maker retorna respostas relevantes, juntamente com uma pontuação de confiança. Essa pontuação indica a confiança de que a resposta é a correspondência ideal da consulta do usuário. 

A pontuação de confiança é um número entre 0 e 100. Uma pontuação 100 é, provavelmente, uma correspondência exata; no entanto, uma pontuação 0 significa que nenhuma resposta correspondente foi encontrada. Quanto maior a pontuação, maior a confiança na resposta. Pode haver várias respostas retornadas para uma determinada consulta. Nesse caso, as respostas são retornadas em uma ordem de pontuação de confiança decrescente.

No exemplo abaixo, você pode ver uma entidade QnA com duas perguntas. 


![Par QnA de exemplo](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

No caso do exemplo acima, você pode esperar pontuações como o intervalo de pontuação de exemplo abaixo para diferentes tipos de consultas de usuário:


![Intervalo de pontuação do classificador](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


A tabela a seguir indica a confiança típica associada a uma determinada pontuação.

|Valor da pontuação|Significado da pontuação|Consulta de exemplo|
|--|--|--|
|90 - 100|Uma correspondência quase exata da consulta de usuário e uma pergunta da KB|"Minhas alterações não são atualizadas na base de conhecimento após a publicação"|
|> 70|Alta confiança – geralmente uma boa resposta que responde por completo à consulta do usuário|"Publiquei minha Base de conhecimento, mas ela não está atualizada"|
|50 - 70|Confiança média – normalmente, uma resposta relativamente boa que deve responder à intenção principal da consulta do usuário|"Devo salvar minhas atualizações antes de publicar minha Base de conhecimento?"|
|30 - 50|Pouca confiança – tipicamente uma resposta relacionada que responde parcialmente à intenção do usuário|"O que salvar e treinar faz?"|
|< 30|Muito pouca confiança – normalmente não responde à consulta do usuário, mas apresenta algumas palavras ou frases correspondentes |"Onde posso adicionar sinônimos à minha Base de conhecimento"|
|0|Nenhuma correspondência, portanto, a resposta não é retornada.|"Quanto custa o serviço"|

## <a name="choose-a-score-threshold"></a>Escolher um limite de pontuação
A tabela acima mostra as pontuações que são esperadas na maioria das Bases de conhecimento. However, since every KB is different, and has different types of words, intents, and goals- we recommend you test and choose the threshold that best works for you. By default the threshold is set to 0, so that all possible answers are returned. The recommended threshold that should work for most KBs, is **50**.

Ao escolher seu limite, tenha em mente o equilíbrio entre Precisão e Cobertura e ajuste seu limite com base em seus requisitos.

- Se **Precisão** for mais importante para seu cenário, aumente seu limite. Dessa forma, sempre que você retornar uma resposta, será muito mais SEGURO e muito mais provável que seja a resposta que os usuários estão procurando. Nesse caso, você pode acabar deixando mais perguntas sem resposta. *Por exemplo:* se seu limite for **70**, é provável que você perca alguns exemplos ambíguos como "o que é salvar e treinar?".

- Se a **Cobertura** (ou o retorno de chamada) for mais importante, e você quiser responder ao maior número de perguntas possível, mesmo que haja apenas uma relação parcial com a pergunta do usuário, então DIMINUA o limite. Isso significa que pode haver mais casos em que a resposta não responda à consulta real do usuário, mas forneça alguma outra resposta relacionada. *For example:* if you make the threshold **30**, you might give answers for queries like "Where can I edit my KB?"

> [!NOTE]
> As versões mais recentes do QnA Maker incluem melhorias na lógica de pontuação e poderão afetar seu limite. Sempre que atualizar o serviço, certifique-se de testar e ajustar o limite, se necessário. Você pode verificar a versão do Serviço QnA [aqui](https://www.qnamaker.ai/UserSettings) e saber como obter as atualizações mais recentes [aqui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Set threshold 

Set the threshold score as a property of the [GenerateAnswer API JSON body](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). This means you set it for each call to GenerateAnswer. 

From the bot framework, set the score as part of the options object with [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) or [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Melhorar as pontuações de confiança
Para melhorar a pontuação de confiança de uma resposta específica a uma consulta de usuário, você poderá adicionar a consulta do usuário à base de dados de conhecimento como uma pergunta alternativa na resposta. Você também pode usar [alterações de palavra](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) que não diferenciam maiúsculas de minúsculas para adicionar sinônimos a palavras-chave em sua base de conhecimento.


## <a name="similar-confidence-scores"></a>Pontuações de confiança semelhantes
Quando várias respostas tiverem uma pontuação de confiança semelhante, é provável que a consulta tenha sido muito genérica e, portanto, com correspondências com probabilidades iguais com várias respostas. Tente estruturar melhor suas perguntas e respostas para que cada entidade QnA tenha uma intenção distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Confidence score differences between test and production
A pontuação de confiança de resposta pode alterar pouco entre o teste e a versão publicada da base de dados de conhecimento, mesmo se o conteúdo for o mesmo. This is because the content of the test and the published knowledge base are located in different Azure Cognitive Search indexes. 

The test index holds all the QnA pairs of your knowledge bases. When querying the test index, the query applies to the entire index then results are restricted to the partition for that specific knowledge base. If the test query results are negatively impacting your ability to validate the knowledge base, you can:
* organize your knowledge base using one of the following:
    * 1 resource restricted to 1 KB: restrict your single QnA resource (and the resulting Azure Cognitive Search test index) to a single knowledge base. 
    * 2 resources - 1 for test, 1 for production: have two QnA Maker resources, using one for testing (with its own test and  production indexes) and one for product (also having its own test and production indexes)
* and, always use the same parameters, such as **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** when querying both your test and production knowledge base

Ao publicar uma base de dados de conhecimento, o conteúdo de perguntas e respostas base de dados de conhecimento é movido do índice de teste para um índice de produção no Azure Search. Veja como funciona a operação [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

If you have a knowledge base in different regions, each region uses its own Azure Cognitive Search index. Como índices diferentes são usados, as pontuações não serão exatamente as mesmas. 


## <a name="no-match-found"></a>Nenhuma correspondência encontrada
Quando nenhuma boa correspondência for encontrada pelo classificador, a pontuação de confiança 0,0 ou "Nenhuma" é retornada e a resposta padrão é "Nenhuma correspondência boa encontrada na KB". You can override this [default response](#change-default-answer) in the bot or application code calling the endpoint. Como alternativa, você também pode definir a resposta de substituição no Azure, e isso altera o padrão para todas as bases de dados de conhecimento implantadas em um determinado serviço QnA Maker.

## <a name="change-default-answer"></a>Alterar a resposta padrão

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

2. Clique para abrir o **Serviço de Aplicativo**.

    ![No portal do Azure, acesse o serviço de aplicativo para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **Configurações do Aplicativo** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Save** (Salvar).

    ![Selecione Configurações de aplicativo e, em seguida, edite DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço Aplicativo

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicativo do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Fontes de dados com suporte](./data-sources-supported.md)

