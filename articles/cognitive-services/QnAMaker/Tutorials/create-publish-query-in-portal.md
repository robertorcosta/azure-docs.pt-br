---
title: 'Tutorial: Criar, publicar e responder no QnA Maker'
titleSuffix: Azure Cognitive Services
description: Crie uma base de dados de conhecimento com perguntas e respostas de perguntas frequentes públicas baseadas na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando do cURL. Em seguida, crie um bot e teste-o com a mesma pergunta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 51d051fee1da1f9bb0c89ea9123748b512f84007
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327972"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Tutorial: No portal do QnA Maker, crie uma base de dados de conhecimento

Crie uma base de dados de conhecimento com perguntas e respostas de perguntas frequentes públicas baseadas na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando do cURL. Em seguida, crie um bot e teste-o com a mesma pergunta. 

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Criar uma base de dados de conhecimento no portal do QnA Maker.
> * Examinar, salvar e treinar a base de dados de conhecimento.
> * Publicar a base de dados de conhecimento.
> * Usar cURL para consultar a base de dados de conhecimento.
> * Criar um bot.
 

> [!NOTE]
> A versão programática deste tutorial está disponível com uma solução completa [ **Azure-Samples/cognitive-services-qnamaker-csharp** no repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento 

1. Entre no portal do [QnA Maker](https://www.qnamaker.ai). 

1. Selecione **Criar uma base de dados de conhecimento** no menu superior.

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore a primeira etapa, porque você usará seu serviço do QnA Maker existente. 

1. Selecione suas configurações existentes:  

    |Configuração|Finalidade|
    |--|--|
    |ID do Diretório do Microsoft Azure|Essa ID está associada à conta que você usa para entrar no portal do Azure e no portal do QnA Maker. |
    |Nome da assinatura do Azure|A conta de cobrança na qual você criou o recurso do QnA Maker.|
    |Serviço QnA do Azure|O recurso existente do QnA Maker.|

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Insira o nome da sua base de dados de conhecimento, `My Tutorial kb`.

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Popule sua base de dados de conhecimento com as seguintes configurações:  

    |Nome da configuração|Valor da configuração|Finalidade|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |O conteúdo das Perguntas frequentes nessa URL é formatado com um ponto de interrogação seguido de uma resposta. O QnA Maker pode interpretar esse formato para extrair perguntas e as respostas associadas.|
    |Arquivo |_não usado neste tutorial_|Isso carrega arquivos para perguntas e respostas. |
    |Personalidade do bate-papo|Amigável|Isso oferece uma [personalidade](../Concepts/best-practices.md#chit-chat) casual e amigável para perguntas e respostas comuns. Você pode editar essas perguntas e respostas mais tarde. |

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Criar sua KB** para concluir o processo de criação.

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Revisar, salvar e treinar a base de dados de conhecimento

1. Examine as perguntas e respostas. A primeira página é de perguntas e respostas da URL. 

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra as perguntas e respostas da Personalidade do bate-papo. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o ícone **Opções de exibição** e, em seguida, **Mostrar metadados**. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de bate-papo têm os metadados **editorial: bate-papo** já definidos. Esses metadados são retornados ao aplicativo cliente, juntamente com a resposta selecionada. O aplicativo cliente, como um chatbot, pode usar esses metadados filtrados para determinar outros processamentos ou interações com o usuário.

    ![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Selecione **Salvar e treinar** na barra de menus superior.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publicar para obter pontos de extremidade da base de dados de conhecimento

Selecione o botão **Publicar** no menu superior. Na página de publicação, selecione **Publicar**.

![Captura de tela do portal do QnA Maker](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Depois que a base de dados de conhecimento for publicada, o ponto de extremidade será exibido.

![Captura de tela das configurações do ponto de extremidade](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Não feche esta página **Publicar**. Você precisará dela posteriormente neste tutorial para criar um bot. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Usar o cURL para consultar uma resposta de perguntas frequentes

1. Selecione a guia **cURL**. 

    ![Captura de tela da guia Curl](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto da guia **Curl** e execute-o em um terminal ou linha de comando habilitado para cURL. O valor do cabeçalho de autorização inclui o texto `Endpoint` com um espaço à direita e, em seguida, a chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Isso parece a pergunta `How large a knowledge base can I create?`, mas não exatamente. O QnA Maker aplica processamento de linguagem natural para determinar se as duas perguntas são iguais.     

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    O QnA Maker tem alguma certeza, com pontuação de 42,81%.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Usar o cURL para consultar uma resposta de bate-papo

1. No terminal habilitado para o cURL, substitua `How large can my KB be?` por uma instrução de fim de conversa do bot feita pelo usuário, como `Thank you`.   

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Como a pergunta `Thank you` correspondeu exatamente a uma pergunta do bate-papo, o QnA Maker está completamente seguro com a pontuação de 100. O QnA Maker também retornou todas as perguntas relacionadas e a propriedade de metadados que contém as informações de marca de metadados de Bate-papo.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Usar o cURL para consultar a resposta padrão

Todas as perguntas que não tenham grau de confiança do QnA Maker recebem a resposta padrão. Esta resposta é configurada no portal do Azure. 

1. No terminal habilitado para cURL, substitua `Thank you` por `x`. 

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    O QnA Maker retornou uma pontuação igual a `0`, o que significa nenhuma confiança. Ele também retornou uma resposta padrão. 

## <a name="create-a-knowledge-base-bot"></a>Criar um bot da base de dados de conhecimento

Para obter mais informações, confira [Criar um chatbot com esta base de dados de conhecimento](create-qna-bot.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com o bot da base de dados de conhecimento, remova o grupo de recursos, `my-tutorial-rg`, para remover todos os recursos do Azure criados no processo do bot.

Quando terminar de usar a base de dados de conhecimento, no portal do QnA Maker, selecione **Minhas bases de dados de conhecimento**. Em seguida, selecione a base de dados de conhecimento **Minha KB de tutorial** e selecione o ícone Excluir na extremidade direita da linha.  

## <a name="next-steps"></a>Próximas etapas

Consulte as [Fontes de dados com suporte](../Concepts/data-sources-supported.md) para obter mais informações sobre os formatos de arquivo com suporte. 

Saiba mais sobre [personalidades](../Concepts/best-practices.md#chit-chat) de bate-papo.

Para obter mais informações sobre a resposta padrão, consulte [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Criar um chatbot com esta base de dados de conhecimento](create-qna-bot.md)