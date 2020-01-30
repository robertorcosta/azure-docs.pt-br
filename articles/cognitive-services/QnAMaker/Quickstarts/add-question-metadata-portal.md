---
title: 'Início Rápido: Adicionar perguntas e respostas no portal do QnA Maker'
titleSuffix: Azure Cognitive Services
description: O início rápido mostra como adicionar conjuntos de perguntas e respostas com metadados para que os usuários possam encontrar a resposta certa para as perguntas deles.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 664d6006ab78f91a8ed0e199cf78fae9512efd73
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843032"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Início Rápido: Adicionar perguntas e respostas com o portal do QnA Maker

Após uma base de dados de conhecimento ser criada, adicione conjuntos de perguntas e respostas com metadados para que os usuários possam encontrar a resposta certa para sua pergunta.

A resposta certa é apenas uma, mas pode haver muitas maneiras de um cliente fazer a pergunta que leva a essa única resposta.

Por exemplo, as perguntas na tabela a seguir são sobre os limites de serviço do Azure, mas cada uma tem a ver com um serviço do Azure diferente.

<a name="qna-table"></a>


|Definir|Perguntas|Resposta|Metadados|
|--|--|--|--|
|Nº 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|Nº 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Após os metadados serem adicionados a um conjunto de perguntas e respostas, o aplicativo cliente pode:

* Solicitar respostas que correspondam apenas a determinados metadados.
* Receber todas as respostas, mas pós-processá-las dependendo dos metadados de cada resposta.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

* Um serviço do QnA Maker
* Uma base de dados de conhecimento criada nesse serviço do QnA Maker

Ambos foram criados no [primeiro início rápido](../how-to/create-knowledge-base.md).

## <a name="sign-in-to-the-qna-maker-portal"></a>Entrar no portal do QnA Maker

1. Entre no [portal do QnA Maker](https://www.qnamaker.ai).

1. Selecione sua base de dados de conhecimento existente. Se você não tiver uma base de dados de conhecimento, volte para o [início rápido anterior](../how-to/create-knowledge-base.md) e conclua as etapas para criar sua base de dados de conhecimento.

## <a name="add-additional-alternatively-phrased-questions"></a>Adicionar outras perguntas com elaboração alternativa

A base de dados de conhecimento atual, do [início rápido anterior](../how-to/create-knowledge-base.md), tem os conjuntos de perguntas e respostas de solução de problemas do QnA Maker. Esses conjuntos foram criados quando a URL foi adicionada à base de dados de conhecimento durante o processo de criação.

Quando essa URL foi importada, apenas uma pergunta com uma resposta foi criada.

Neste procedimento, adicione outras perguntas.

1. Na página **Editar**, use a caixa de texto de pesquisa acima dos conjuntos de perguntas e respostas para encontrar a pergunta `How large a knowledge base can I create?`

1. Na coluna **Pergunta**, selecione **+ Adicionar frase alternativa** e adicione cada frase alternativa fornecida na tabela a seguir.

    |Frase alternativa|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecione **Salvar e treinar** para treinar a base de dados de conhecimento novamente.

1. Selecione **Testar** e, em seguida, insira uma pergunta que seja semelhante a uma das novas frases alternativas, mas não exatamente igual:

    `What GB size can a knowledge base be?`

    A resposta correta é retornada no formato de markdown: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Se selecionar **Inspecionar** sob a resposta retornada, você poderá ver mais respostas que correspondem à pergunta, mas não com o mesmo nível elevado de confiança.

    Não adicione todas as combinações possíveis de frases alternativas. Acione o [aprendizado ativo](../how-to/improve-knowledge-base.md) do QnA Maker, que encontra as frases alternativas que mais ajudarão sua base de dados de conhecimento a atender às necessidades de seus usuários.

1. Selecione **Testar** novamente para fechar a janela de teste.

## <a name="add-metadata-to-filter-the-answers"></a>Adicionar metadados para filtrar as respostas

Adicionar metadados a um conjunto de perguntas e respostas permite que o aplicativo cliente solicite respostas filtradas. Esse filtro é aplicado antes que o [primeiro e o segundo classificadores](../concepts/query-knowledge-base.md#ranker-process) sejam aplicados.

1. Adicione o segundo conjunto de perguntas e respostas, sem os metadados, da [primeira tabela neste início rápido](#qna-table) e continue com as etapas a seguir.

1. Selecione **Opções de exibição** e, em seguida, selecione **Mostrar metadados**.

1. Para o conjunto de perguntas e respostas que você acabou de adicionar, selecione **Adicionar marcas de metadados** e, em seguida, adicione o nome `service` e o valor de `search`, `service:search`.

1. Adicione outras marcas de metadados com o nome `link_in_answer` e o valor de `false`, `link_in_answer:false`.

1. Pesquise a primeira resposta na tabela, `How large a knowledge base can I create?`.
1. Adicione pares de metadados para as mesmas duas marcas de metadados:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Agora, você tem duas perguntas com as mesmas marcas de metadados com valores diferentes.

1. Selecione **Salvar e treinar** para treinar a base de dados de conhecimento novamente.

1. Selecione **Publicar** no menu superior para ir para a página de publicação.
1. Selecione o botão **Publicar** para publicar a base de dados de conhecimento atual em um ponto de extremidade passível de consulta.
1. Após a base de dados de conhecimento ser publicada, selecione a guia **Ondulação** para ver um exemplo de comando cURL usado para gerar uma resposta da base de dados de conhecimento.
1. Copie o comando para um bloco de notas ou outro ambiente editável para que você possa editá-lo. Edite para inserir seu próprio nome de recurso, ID da base de dados de conhecimento e chave do ponto de extremidade:

    |Substitua|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Observe que a pergunta é apenas uma única palavra, `size`, que pode retornar qualquer conjunto de perguntas e respostas. A matriz `strictFilters` instrui a resposta a reduzir para apenas as respostas `qna_maker`.

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. A resposta inclui apenas a resposta que atende aos critérios de filtro.

    A seguinte resposta cURL foi formatada para facilitar a leitura:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Se houver um conjunto de perguntas e respostas que não correspondeu ao termo de pesquisa, mas que correspondeu ao filtro, ele não será retornado. Em vez disso, a resposta geral `No good match found in KB.` será retornada.

    Certifique-se de manter seus pares de nome e valor de metadados dentro dos limites necessários.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter resposta com o Postman ou a cURL](get-answer-from-knowledge-base-using-url-tool.md)