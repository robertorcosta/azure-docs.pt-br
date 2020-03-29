---
title: Migrar bases de dados de conhecimento – QnA Maker
description: Migrar uma base de dados de conhecimento requer a exportação de uma base de dados de conhecimento e a importação para outra.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258083"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento usando export-import

Migração é o processo de criação de uma nova base de conhecimento a partir de uma base de conhecimento existente. Você pode fazer isso por várias razões:

* processo de backup e restauração
* Gasoduto CI/CD
* mover regiões

A migração de uma base de conhecimento requer a exportação de uma base de conhecimento existente e, em seguida, importar para outra.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Configurar um novo [serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Entre no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de conhecimento de origem que deseja migrar.

1. Na página **Configurações,** selecione **Exportar base de conhecimento** para baixar um arquivo .tsv que contenha o conteúdo de sua base de conhecimento de origem - perguntas, respostas, metadados, solicitações de acompanhamento e os nomes de origem dos dados dos quais foram extraídos.

1. Selecione **Criar uma base** de conhecimento a partir do menu superior e, em seguida, criar uma base de conhecimento _vazia._ Ele está vazio porque quando você criá-lo, você não vai adicionar quaisquer URLs ou arquivos. Elas são adicionadas durante a etapa de importação, após a criação.

    Configure a base de conhecimento. Defina apenas o novo nome base de conhecimento. Nomes duplicados e caracteres especiais têm suporte.

    Não selecione nada da Etapa 4 porque esses valores serão substituídos quando você importar o arquivo.

1. No passo 5, selecione **Criar**.

1. Nessa nova base de dados de conhecimento, abra a guia **Configurações** e selecione **Importar base de dados de conhecimento**. Isso importa as perguntas, respostas, metadados, solicitações de acompanhamento e retém os nomes de origem dos dados dos quais foram extraídos.

   > [!div class="mx-imgBorder"]
   > [![Importar base de dados de conhecimento](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de dados de conhecimento usando o painel de teste. Saiba como [testar a base de dados de conhecimento](../How-To/test-knowledge-base.md).

1. **Publique** a base de conhecimento e crie um bot de bate-papo. Saiba como [publicar a base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migrar programáticamente uma base de conhecimento do QnA Maker

O processo de migração está programáticamente disponível usando as seguintes APIs REST:

**Exportar**

* [Baixe a API da base de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importar**

* [Substitua a API (recarregar com a mesma ID base de conhecimento)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Criar API (carregar com nova base de conhecimento ID)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Logs de chat e alterações
As alterações (sinônimos) que não diferenciam maiúsculas de minúsculas não são importadas automaticamente. Use as [APIs V4](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de conhecimento.

Não há nenhuma maneira de migrar os logs de chat, já que a nova base de dados de conhecimento usa o Application Insights para armazenar os logs de chat.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
