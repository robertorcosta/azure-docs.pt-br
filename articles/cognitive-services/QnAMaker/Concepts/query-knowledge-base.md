---
title: Consultar a base de conhecimento - QnA Maker
description: Uma base de conhecimento deve ser publicada. Uma vez publicado, a base de conhecimento é consultada no ponto final da previsão de tempo de execução usando a API gerar Resposta.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220716"
---
# <a name="query-the-knowledge-base-for-answers"></a>Consultar a base de conhecimento para respostas

Uma base de conhecimento deve ser publicada. Uma vez publicado, a base de conhecimento é consultada no ponto final da previsão de tempo de execução usando a API gerar Resposta. A consulta inclui o texto de pergunta e outras configurações para ajudar o QnA Maker a selecionar a melhor correspondência possível para uma resposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como o QnA Maker processa uma consulta de usuário para selecionar a melhor resposta

A base de conhecimento qnA Maker treinada e [publicada](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) recebe uma consulta do usuário, a partir de um bot ou outro aplicativo cliente, na [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama a seguir ilustra o processo quando a consulta do usuário é recebida.

![O processo de modelo de classificação para uma consulta de usuário](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo de ranker

O processo é explicado na tabela a seguir.

|Etapa|Finalidade|
|--|--|
|1|O aplicativo cliente envia a consulta do usuário para a [API GerarResposta](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|O QnA Maker préprocessa a consulta do usuário com detecção de idiomas, soletradores e disjuntores de palavras.|
|3|Este pré-processamento é tomado para alterar a consulta do usuário para os melhores resultados de pesquisa.|
|4|Esta consulta alterada é enviada para um Índice de `top` Pesquisa Cognitiva do Azure, que recebe o número de resultados. Se a resposta correta não estiver nesses resultados, aumente o valor de `top` um pouco. Geralmente, um valor de `top` 10 para obras em 90% das consultas.|
|5|QnA Maker usa featurização baseada em sintática e semântica para determinar a semelhança entre a consulta do usuário e os resultados de QnA buscados.|
|6|O modelo de ranker aprendido por máquina usa as diferentes características, desde o passo 5, para determinar as pontuações de confiança e a nova ordem de classificação.|
|7|Os novos resultados são devolvidos ao aplicativo do cliente em ordem ranqueada.|
|||

Os recursos usados incluem, mas não se limitam à semântica em nível de palavra, importância no nível do termo em um corpus e modelos semânticos profundos para determinar a semelhança e relevância entre duas strings de texto.

## <a name="http-request-and-response-with-endpoint"></a>Solicitação e resposta HTTP com endpoint
Quando você publica sua base de conhecimento, o serviço cria um ponto final HTTP baseado em REST que pode ser integrado ao seu aplicativo, geralmente um bot de bate-papo.

### <a name="the-user-query-request-to-generate-an-answer"></a>A solicitação de consulta do usuário para gerar uma resposta

Uma consulta de usuário é a pergunta que o usuário `How do I add a collaborator to my app?`final faz da base de conhecimento, como . A consulta é muitas vezes em um formato de linguagem natural `help with collaborators`ou algumas palavras-chave que representam a questão, como . A consulta é enviada para sua base de conhecimento a partir de uma solicitação HTTP em seu aplicativo de cliente.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Você controla a resposta definindo propriedades como [scoreThreshold,](./confidence-score.md#choose-a-score-threshold) [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Use [o contexto da conversa](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) com funcionalidade [multi-turn](../how-to/multiturn-conversation.md) para manter a conversa indo refinar as perguntas e respostas, para encontrar a resposta correta e final.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A resposta de uma chamada para gerar uma resposta

A resposta HTTP é a resposta recuperada da base de conhecimento, com base na melhor correspondência para uma determinada consulta de usuário. A resposta inclui a resposta e o escore de previsão. Se você pediu mais de uma `top` resposta superior com a propriedade, você terá mais de uma resposta superior, cada uma com uma pontuação.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)
