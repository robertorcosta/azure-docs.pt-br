---
title: Migrar bases de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902030"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Entre no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de dados de conhecimento de origem que você deseja migrar.

1. Na página **configurações** , selecione **Exportar base de dados de conhecimento** para baixar um arquivo. tsv que contém o conteúdo de sua base de dados de conhecimento de origem-perguntas, respostas, metadados, avisos de acompanhamento e os nomes da fonte de dados da qual eles foram extraídos.

1. Selecione **criar uma base de dados de conhecimento** no menu superior e, em seguida, crie uma base de dados de conhecimento _vazia_ . Ele está vazio porque, ao criá-lo, você não adicionará URLs ou arquivos. Eles são adicionados durante a etapa de importação, após a criação.

    Configure a base de dados de conhecimento. Defina o novo nome da base de dados de conhecimento apenas. Nomes duplicados e caracteres especiais têm suporte.

    Não selecione nada na etapa 4 porque esses valores serão substituídos quando você importar o arquivo.

1. Na etapa 5, selecione **criar**.

1. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e selecione **Importar base de dados de conhecimento**. Isso importa as perguntas, as respostas, os metadados, os prompts de acompanhamento e retém os nomes das fontes de dados dos quais eles foram extraídos.

   > [!div class="mx-imgBorder"]
   > [![importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).
1. **Publicar** a base de dados de conhecimento. Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Use o ponto de extremidade no aplicativo ou código de bot. Veja aqui como [Criar um bot de QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Neste ponto, todo o conteúdo da base de dados de conhecimento – perguntas, respostas e metadados, juntamente com os nomes dos arquivos de origem e as URLs, são importados para a nova base de dados de conhecimento.

## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) que não diferenciam maiúsculas de minúsculas não são importadas automaticamente. Use as [APIs v4](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de dados de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
