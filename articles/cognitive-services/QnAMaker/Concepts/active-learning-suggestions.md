---
title: Sugestões ativas de aprendizagem - QnA Maker
description: Sugestões de aprendizagem ativas permitem que você melhore a qualidade de sua base de conhecimento, sugerindo perguntas alternativas, baseadas em submissões de usuários, ao seu par de perguntas e respostas.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: af4f6b399bfd537b38ea741d03e59371ee81e588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053150"
---
# <a name="active-learning-suggestions"></a>Sugestões ativas de aprendizagem

O recurso _de sugestões de aprendizagem ativa_ permite que você melhore a qualidade de sua base de conhecimento, sugerindo perguntas alternativas, baseadas em submissões de usuários, ao seu par de perguntas e respostas. Você examina essas sugestões, adicionando-as às perguntas existentes ou rejeitando-as.

Sua base de dados de conhecimento não é alterada automaticamente. Para que qualquer mudança tenha efeito, você deve aceitar as sugestões. Essas sugestões adicionam perguntas, mas não alteram nem removem perguntas existentes.

## <a name="what-is-active-learning"></a>O que é aprendizado ativo?

O QnA Maker aprende novas variações de pergunta com comentários implícitos e explícitos.

* [Feedback implícito](#how-qna-makers-implicit-feedback-works) – O ranker entende quando uma pergunta do usuário tem múltiplas respostas com pontuações muito próximas e considera isso como feedback. Você não precisa fazer nada para que isso aconteça.
* [Feedback explícito](#how-you-give-explicit-feedback-with-the-train-api) – Quando múltiplas respostas com pouca variação de pontuação são devolvidas da base de conhecimento, o aplicativo do cliente pergunta ao usuário qual pergunta é a pergunta correta. O feedback explícito do usuário é enviado ao QnA Maker com a [API do Trem](../How-to/improve-knowledge-base.md#train-api).

Ambos os métodos fornecem ao ranker consultas semelhantes que estão agrupadas.

## <a name="how-active-learning-works"></a>Como o aprendizado ativo funciona

O aprendizado ativo é acionado com base nas pontuações das poucas respostas retornadas pelo QnA Maker. Se as diferenças de pontuação entre os conjuntos de QnA que correspondem à consulta estão dentro de um pequeno intervalo, então a consulta é considerada uma possível sugestão (como uma pergunta alternativa) para cada um dos possíveis pares de QnA. Uma vez que você aceita a pergunta sugerida para um par qnA específico, ela é rejeitada para os outros pares. Você precisa se lembrar de salvar e treinar, depois de aceitar sugestões.

O aprendizado ativo fornece as melhores sugestões possíveis em casos em que os pontos de extremidade estão obtendo uma quantidade e variedade razoáveis de consultas de uso. Quando 5 ou mais consultas semelhantes são agrupadas, a cada 30 minutos, o QnA Maker sugere que as perguntas baseadas no usuário ao designer da base de conhecimento aceitem ou rejeitem. Todas as sugestões são clusterizadas por similaridade e as principais sugestões de perguntas alternativas são exibidas com base na frequência das consultas específicas por usuários finais.

Uma vez que as perguntas são sugeridas no portal QnA Maker, você precisa rever e aceitar ou rejeitar essas sugestões. Não há uma API para gerenciar sugestões.

## <a name="turn-on-active-learning"></a>Ativar o aprendizado ativo

Por padrão, o aprendizado ativo está **desligado.**
Para usar o aprendizado ativo:
* Você precisa ativar o [aprendizado ativo](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) para que o QnA Maker colete perguntas alternativas para sua base de conhecimento.
* Para ver as perguntas alternativas sugeridas, [use opções de exibição](../How-To/improve-knowledge-base.md#view-suggested-questions) na página Editar.

## <a name="how-qna-makers-implicit-feedback-works"></a>Como funciona o feedback implícito do QnA Maker

O feedback implícito do QnA Maker usa um algoritmo para determinar a proximidade da pontuação e, em seguida, faz sugestões ativas de aprendizado. O algoritmo para determinar a proximidade não é um cálculo simples. As faixas no exemplo a seguir não devem ser corrigidas, mas devem ser usadas como um guia para entender apenas o impacto do algoritmo.

Quando a pontuação de uma pergunta tem um alto grau de confiabilidade, tal como 80%, o intervalo de pontuações que são consideradas para aprendizado ativo é amplo, dentro de aproximadamente 10%. Conforme a pontuação de confiança diminui, por exemplo, para 40%, o intervalo de pontuações diminui também, para dentro de aproximadamente 4%.

Na seguinte resposta json de uma consulta ao QnA Maker's generateAnswer, as pontuações para A, B e C estão próximas e seriam consideradas como sugestões.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker não saberá qual resposta é a melhor resposta. Use a lista de sugestões do portal QnA Maker para selecionar a melhor resposta e treinar novamente.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Como você dá feedback explícito com a API do trem

QnA Maker precisa de feedback explícito sobre qual das respostas foi a melhor resposta. Como a melhor resposta é determinada depende de você e pode incluir:

* Feedback do usuário, selecionando uma das respostas.
* Lógica de negócios, como determinar uma faixa de pontuação aceitável.
* Uma combinação de feedback do usuário e lógica de negócios.

Use a [API do trem](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) para enviar a resposta correta ao QnA Maker, depois que o usuário selecioná-la.

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Consultar a base de conhecimento](query-knowledge-base.md)