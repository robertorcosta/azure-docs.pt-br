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
ms.openlocfilehash: 942932c229ace82a0bf66da7a5421f936b028088
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302552"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

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

1. **Publique** a base de dados de conhecimento e crie um bot de chat. Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) que não diferenciam maiúsculas de minúsculas não são importadas automaticamente. Use as [APIs v4](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de dados de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
