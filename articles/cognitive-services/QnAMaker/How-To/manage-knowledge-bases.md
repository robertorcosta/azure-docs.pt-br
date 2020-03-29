---
title: Gerenciar bases de conhecimento - QnA Maker
description: O QnA Maker permite que você gerencie suas bases de conhecimento fornecendo acesso às configurações da base de conhecimento e conteúdo.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 57839125011016daed5f0b3d441a83e8db488198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071139"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Crie base de conhecimento e gerencie configurações

O QnA Maker permite que você gerencie suas bases de conhecimento fornecendo acesso às configurações da base de conhecimento e às fontes de dados.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
> * Um [recurso QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) criado no portal Azure. Lembre-se de sua ID e sua assinatura do Azure Active Directory, bem como do nome do recurso do QnA que você selecionou ao criar o recurso.

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

1. Entre no portal [QnAMaker.ai](https://QnAMaker.ai) com suas credenciais do Azure.

1. No portal do QnA Maker, selecione **Criar uma base de dados de conhecimento**.

1. Na página **Criar**, pule a **Etapa 1** caso já tenha o recurso do QnA Maker.

    Se você ainda não criou o recurso, selecione **Criar um serviço do QnA**. Você será direcionado para o [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar o serviço do QnA Maker na sua assinatura. Lembre-se de sua ID e sua assinatura do Azure Active Directory, bem como do nome do recurso do QnA que você selecionou ao criar o recurso.

    Quando terminar de criar o recurso no portal do Azure, retorne ao portal do QnA Maker, atualize a página do navegador e prossiga para a **Etapa 2**.

1. Na **Etapa 3**, selecione a sua assinatura do Active Directory, o serviço (recurso) e o idioma para todas as bases de dados de conhecimento criadas no serviço.

   ![Captura de tela da seleção de uma base de dados de conhecimento do serviço do QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Na **Etapa 3,** nomeie sua base `My Sample QnA KB`de conhecimento.

1. Na **Etapa 4**, defina as configurações com a seguinte tabela:

    |Configuração|Valor|
    |--|--|
    |**Habilitar a extração de vários turnos de URLs, arquivos .pdf ou .docx.**|Verificado|
    |**Texto de resposta padrão**| `Quickstart - default answer not found.`|
    |**+ Adicionar URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Bate-papo**|Selecione **Professional**|

1. Na **Etapa 5**, selecione **Criar sua base de dados**.

    O processo de extração leva alguns instantes para ler o documento e identificar perguntas e respostas.

    Depois que o QnA Maker criar com êxito a base de dados de conhecimento, a página **Base de dados de conhecimento** será aberta. Você pode editar o conteúdo da base de dados de conhecimento nesta página.

## <a name="edit-knowledge-base"></a>Editar a base de dados de conhecimento

1.  Selecione **Minhas bases de dados de conhecimento** na barra de navegação superior.

       Você pode ver todos os serviços que você criou ou compartilhou classificados em ordem decrescente da data da **última modificação**.

       ![Minhas Bases de Dados de Conhecimento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Selecione uma base de dados de conhecimento específica para fazer edições nela.

1.  Selecione **Configurações**. A lista a seguir contém campos que você pode alterar.

       |Goal|Ação|
       |--|--|
       |Adicionar URL|Você pode adicionar novos URLs para adicionar novos conteúdos de perguntas frequentes à base de conhecimento clicando em **Gerenciar base de conhecimento -> link "+ Adicionar URL"**.|
       |Excluir URL|Você pode excluir URLs existentes selecionando o ícone de exclusão, a lixeira.|
       |Atualizar conteúdo|Se você quiser que sua base de conhecimento rastreie o conteúdo mais recente de URLs existentes, marque a caixa de seleção **Atualizar**. Isso atualizará a base de conhecimento com o conteúdo de URL mais recente uma vez. Isso não está definindo um cronograma regular de atualizações.|
       |Adicionar arquivo|Você pode adicionar um documento de arquivo suportado para fazer parte de uma base de conhecimento, selecionando **Gerenciar base de conhecimento** e, em seguida, selecionando **+ Adicionar Arquivo**|
    |Importar|Você também pode importar qualquer base de conhecimento existente selecionando o botão **base de conhecimento de importação.** |
    |Atualizar|A atualização da base de conhecimento depende do **nível de preço de gerenciamento** usado durante a criação do serviço QnA Maker associado à sua base de conhecimento. Você também pode atualizar o nível de gerenciamento do portal Azure, se necessário.

  1. Depois de terminar de fazer alterações na base de conhecimento, **selecione Salvar e treine** no canto superior direito da página, a fim de persistir as alterações.

       ![Salvar e Treinar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Se você sair da página antes de selecionar **salvar e treinar**, todas as alterações serão perdidas.



## <a name="manage-large-knowledge-bases"></a>Gerenciar grandes bases de conhecimento

* **Grupos de origem de dados**: Os QnAs são agrupados pela fonte de dados da qual foram extraídos. Você pode expandir ou recolher a fonte de dados.

    ![Usar a barra de fonte de dados do QnA Maker para recolher e expandir perguntas e respostas da fonte de dados](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Busque base de conhecimento**: Você pode pesquisar a base de conhecimento digitando na caixa de texto no topo da tabela Base de Conhecimento. Clique em entrar para pesquisar a pergunta, a resposta ou o conteúdo de metadados. Clique no ícone X para remover o filtro de pesquisa.

    ![Usar a caixa de pesquisa do QnA Maker acima das perguntas e respostas para reduzir a exibição apenas para itens correspondentes ao filtro](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginação**: Mova-se rapidamente através de fontes de dados para gerenciar grandes bases de conhecimento

    ![Usar os recursos de paginação do QnA Maker acima das perguntas e respostas para percorrer as páginas de perguntas e respostas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Excluir as bases de dados de conhecimento

Excluir uma base de conhecimento (KB) é uma operação permanente. Não pode ser desfeito. Antes de excluir uma base de conhecimento, você deverá exportar a base de dados de conhecimento da página **Configurações** do portal do QnA Maker.

Se você compartilhar sua base de conhecimento com colaboradores,](colabore-conhecimento-base.md) e depois exclua-a, todos perdem o acesso ao KB.

## <a name="next-steps"></a>Próximas etapas

Aprenda a [gerenciar a linguagem](language-knowledge-base.md) de todas as bases de conhecimento em um recurso.

* Editar conjuntos qnA
* Gerenciar os recursos do Azure usados pelo QnA Maker