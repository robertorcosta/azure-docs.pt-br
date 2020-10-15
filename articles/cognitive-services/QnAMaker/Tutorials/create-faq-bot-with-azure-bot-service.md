---
title: 'Tutorial: criar um bot de perguntas frequentes com o Serviço de Bot do Azure'
description: Neste tutorial, crie um bot de perguntas frequentes sem código com o QnA Maker e o Serviço de Bot do Azure.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777401"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Tutorial: criar um bot de perguntas frequentes com o Serviço de Bot do Azure
Crie um bot de perguntas frequentes com o QnA Maker e o [Serviço de Bot](https://azure.microsoft.com/en-us/services/bot-service/) do Azure sem código.

Neste tutorial, você aprenderá como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Vincular uma base de dados de conhecimento do QnA Maker a um Serviço de Bot do Azure
> * Implantar o bot
> * Bater papo com o bot no Webchat
> * Disponibilizar o bot entre os canais compatíveis

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada com sucesso, você chegará à página abaixo.

![Captura de tela da publicação bem-sucedida](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Criar um bot

Após a publicação, é possível criar um bot na página **Publicar**:

* Você pode criar vários bots rapidamente, todos apontando para a mesma base de dados conhecimento para diferentes regiões ou planos de preço para os bots individuais.
* Se você quiser apenas um bot para a base de dados de conhecimento, use o link **Exibir todos os bots no portal do Azure** para exibir uma lista de seus bots atuais.

Quando você faz alterações na base de dados de conhecimento e republica, não é necessário fazer mais nada com o bot. Ele já está configurado para trabalhar com a base de dados de conhecimento e trabalha com todas as alterações futuras na base de dados de conhecimento. Sempre que você publica uma base de dados de conhecimento, todos os bots conectados a ela são atualizados automaticamente.

1. No portal do QnA Maker, na página **Publicar**, selecione **Criar bot**. Esse botão será exibido somente depois que você tiver publicado a base de dados de conhecimento.

    ![Captura de tela da criação de um bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. É aberta uma nova guia do navegador para o portal do Azure com a página de criação do Serviço de Bot do Azure. Configure o serviço de bot do Azure. O bot e o QnA Maker podem compartilhar o plano do serviço de aplicativo Web, mas não podem compartilhar o aplicativo Web. Isso significa que o **nome do aplicativo** para o bot deve ser diferente do nome do aplicativo usado para o serviço do QnA Maker.

    * **Deve ser feito**
        * Alterar o identificador de bot se ele não for exclusivo.
        * Selecionar a linguagem de programação do SDK. Depois que o bot for criado, você poderá baixar o código em seu ambiente de desenvolvimento local e continuar o processo de desenvolvimento.
    * **O que não fazer**
        * Alterar as configurações a seguir no portal do Azure ao criar o bot. Elas são preenchidas previamente para sua base de dados de conhecimento existente:
           * Chave de autenticação do QnA
           * Plano e local do serviço de aplicativo


1. Depois que o bot for criado, abra o recurso **Serviço de bot**.
1. Em **Gerenciamento de Bot**, selecione **Testar no Webchat**.
1. No prompt de chat de **Digite sua mensagem**, digite:

    `Azure services?`

    O chatbot responde com uma resposta de sua base de dados de conhecimento.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Insira uma consulta de usuário no Webchat de teste.":::
1. Disponibilizar o bot entre os [canais compatíveis](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true) adicionais.
