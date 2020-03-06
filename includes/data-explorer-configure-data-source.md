---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305054"
---
## <a name="configure-the-data-source"></a>Configurar a fonte de dados

Execute as etapas a seguir para configurar o Azure Data Explorer como uma fonte de dados para sua ferramenta de painel. Abordaremos essas etapas com mais detalhes nesta seção:

1. Crie uma entidade de serviço do Azure AD (Azure Active Directory). A entidade de serviço é usada pela ferramenta de painel para acessar o serviço de Data Explorer do Azure.

1. Adicione a entidade de serviço do Azure AD à função *visualizadores* no banco de dados do Azure Data Explorer.

1. Especifique suas propriedades de conexão da ferramenta de painel com base nas informações da entidade de serviço do Azure AD e teste a conexão.

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

É possível criar a entidade de serviço no [portal do Azure](#azure-portal) ou usando a experiência de linha de comando da [CLI do Azure](#azure-cli). Independentemente do método usado, após a criação você obterá valores para quatro propriedades de conexão que serão usadas em etapas posteriores.

#### <a name="azure-portal"></a>Portal do Azure

1. Para criar a entidade de serviço, siga as instruções na [documentação do portal do Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Na seção [Atribuir o aplicativo a uma função](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application), atribua um tipo de função **Leitor** ao cluster do Azure Data Explorer.

    1. Na seção [obter valores para entrar](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , copie os três valores de propriedade abordados nas etapas: **ID do diretório** (ID do locatário), **ID do aplicativo**e **senha**.

1. No portal do Azure, selecione **Assinaturas** e copie a ID da assinatura na qual você criou a entidade de serviço.

    ![ID da assinatura - portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>CLI do Azure

1. Crie uma entidade de serviço. Defina um escopo apropriado e um tipo de função `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Para obter mais informações, consulte [Criar uma entidade de serviço do Azure com CLI do Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

1. O comando retorna um conjunto de resultados semelhante ao seguinte. Copie os três valores de propriedade: **appID**, **password** e **tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenha uma lista das suas assinaturas.

    ```azurecli
    az account list --output table
    ```

    Copie a ID de assinatura apropriado.

    ![ID da assinatura - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adicionar a entidade de serviço à função visualizadores

Agora que você tem uma entidade de serviço, adicione-a à função *visualizadores* no banco de dados do Azure Data Explorer. É possível executar essa tarefa em **Permissões** no portal do Azure ou em **Consultar**, usando um comando de gerenciamento.

#### <a name="azure-portal---permissions"></a>Portal do Azure - Permissões

1. No portal do Azure, acesse o cluster do Azure Data Explorer.

1. Na seção **Visão geral**, selecione o banco de dados com os dados de exemplo do StormEvents.

    ![Selecionar banco de dados](media/data-explorer-configure-data-source/select-database.png)

1. Selecione **Permissões** e, em seguida, **Adicionar**.

    ![Permissões de banco de dados](media/data-explorer-configure-data-source/database-permissions.png)

1. Em **Adicionar permissões de banco de dados**, selecione o **Visualizador** e, em seguida, **Selecionar entidades**.

    ![Adicionar permissões de banco de dados](media/data-explorer-configure-data-source/add-permission.png)

1. Pesquise a entidade de serviço que você criou. Selecione a entidade de serviço e, em seguida **Selecionar**.

    ![Gerenciar permissões no portal do Azure](media/data-explorer-configure-data-source/new-principals.png)

1. Selecione **Salvar**.

    ![Gerenciar permissões no portal do Azure](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Comando de gerenciamento - Consultar

1. No portal do Azure, acesse o cluster do Azure Data Explorer e selecione **Consultar**.

    ![Query](media/data-explorer-configure-data-source/query.png)

1. Execute o seguinte comando na janela de consulta. Use a ID de aplicativo e o ID de locatário do portal do Azure ou CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    O comando retorna um conjunto de resultados semelhante ao seguinte. Neste exemplo, a primeira linha é para um usuário existente no banco de dados e a segunda linha é para a entidade de serviço que acabou de ser adicionada.

    ![Conjunto de resultados](media/data-explorer-configure-data-source/result-set.png)
