---
title: 'Início Rápido: Adicionar perguntas e respostas no portal do QnA Maker'
description: O início rápido mostra como adicionar conjuntos de perguntas e respostas com metadados para que os usuários possam encontrar a resposta certa para as perguntas deles.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 25c0fe549dfc850a53b06f79f348a87cba3b70a1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109937"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Início Rápido: Adicionar perguntas e respostas com o portal do QnA Maker

Depois que uma base de dados de conhecimento for criada, adicione conjuntos de QnA (perguntas e respostas) com metadados para filtrar a resposta. As perguntas da tabela a seguir referem-se aos limites de serviço do Azure, mas cada uma diz respeito a um serviço do Azure diferente.

<a name="qna-table"></a>

|Definir|Perguntas|Resposta|Metadados|
|--|--|--|--|
|Nº 1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|Nº 2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Depois que os metadados forem adicionados a um conjunto de QnA, o aplicativo cliente poderá:

* Solicitar respostas que correspondam apenas a determinados metadados.
* Receber todas as respostas, mas pós-processá-las dependendo dos metadados de cada resposta.


## <a name="prerequisites"></a>Pré-requisitos

* Concluir o [início rápido anterior](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Entrar no portal do QnA Maker

1. Entre no [portal do QnA Maker](https://www.qnamaker.ai).

1. Selecione a base de dados de conhecimento existente do [início rápido anterior](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Adicionar outras perguntas com elaboração alternativa

A base de dados de conhecimento atual tem os conjuntos de QnA de solução de problemas do QnA Maker. Esses conjuntos foram criados quando a URL foi adicionada à base de dados de conhecimento durante o processo de criação.

Quando essa URL foi importada, apenas uma pergunta com uma resposta foi criada. Neste procedimento, adicione outras perguntas.

1. Na página **Editar**, use a caixa de texto de pesquisa acima dos conjuntos de perguntas e respostas para encontrar a pergunta `How large a knowledge base can I create?`

1. Na coluna **Pergunta**, selecione **+ Adicionar frase alternativa** e adicione cada frase alternativa fornecida na tabela a seguir.

    |Frase alternativa|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecione **Salvar e treinar** para treinar a base de dados de conhecimento novamente.

1. Selecione **Testar** e, em seguida, insira uma pergunta que seja semelhante a uma das novas frases alternativas, mas não exatamente igual:

    `What GB size can a knowledge base be?`

    A resposta correta é retornada no formato markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Se selecionar **Inspecionar** sob a resposta retornada, você poderá ver mais respostas que correspondem à pergunta, mas não com o mesmo nível elevado de confiança.

    Não adicione todas as combinações possíveis de frases alternativas. Quando você ativa o [aprendizado ativo](../how-to/improve-knowledge-base.md) do QnA Maker, isso encontra as frases alternativas que mais ajudarão a base de dados de conhecimento a atender às necessidades dos usuários.

1. Selecione **Testar** novamente para fechar a janela de teste.

## <a name="add-metadata-to-filter-the-answers"></a>Adicionar metadados para filtrar as respostas

Adicionar metadados a um conjunto de perguntas e respostas permite que o aplicativo cliente solicite respostas filtradas. Esse filtro é aplicado antes que o [primeiro e o segundo classificadores](../concepts/query-knowledge-base.md#ranker-process) sejam aplicados.

1. Adicione o segundo conjunto de perguntas e respostas, sem os metadados, da [primeira tabela neste início rápido](#qna-table) e continue com as etapas a seguir.

1. Selecione **Opções de exibição** e, em seguida, selecione **Mostrar metadados**.

1. Para o conjunto de QnA recém-adicionado, selecione **Adicionar marcas de metadados** e, em seguida, adicione o nome `service` e o valor `search`. Ele tem esta aparência: `service:search`.

1. Adicione outras marcas de metadados com o nome `link_in_answer` e o valor `false`. Ele tem esta aparência: `link_in_answer:false`.

1. Pesquise a primeira resposta na tabela, `How large a knowledge base can I create?`.

1. Adicione pares de metadados para as mesmas duas marcas de metadados:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Agora, você tem duas perguntas com as mesmas marcas de metadados com valores diferentes.

1. Selecione **Salvar e treinar** para treinar a base de dados de conhecimento novamente.

1. Selecione **Publicar** no menu superior para ir para a página de publicação.
1. Selecione o botão **Publicar** para publicar a base de dados de conhecimento atual em um ponto de extremidade.
1. Depois que a base de dados de conhecimento for publicada, prossiga para o próximo início rápido para saber como gerar uma resposta usando a base de dados de conhecimento.

## <a name="what-did-you-accomplish"></a>O que você realizou?

Você editou sua base de dados de conhecimento para dar suporte a mais perguntas e forneceu pares nome/valor para dar suporte à filtragem durante a pesquisa da resposta principal ou do pós-processamento, depois que as respostas são retornadas.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não for prosseguir para o próximo início rápido, exclua os recursos do QnA Maker e do Bot Framework no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter resposta com o Postman ou a cURL](get-answer-from-knowledge-base-using-url-tool.md)