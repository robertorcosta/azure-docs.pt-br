---
title: Gerenciar o acesso a workspaces, dados e pipelines
description: Saiba como gerenciar o controle de acesso a workspaces, dados e pipelines em um workspace do Azure Synapse Analytics (versão prévia).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: a0fbcab194b90bbe89948fee1efb604266dbbb0f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311750"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gerenciar o acesso a workspaces, dados e pipelines

Saiba como gerenciar o controle de acesso a workspaces, dados e pipelines em um workspace do Azure Synapse Analytics (versão prévia).

> [!NOTE]
> Para GA, o Azure RBAC será mais desenvolvido por meio da introdução das funções do Azure específicas do Synapse

## <a name="access-control-for-workspace"></a>Controle de acesso ao workspace

Para uma implantação de produção em um workspace do Azure Synapse, sugerimos organizar seu ambiente para facilitar o provisionamento de usuários e administradores.

> [!NOTE]
> A abordagem utilizada aqui é criar vários grupos de segurança e configurar o workspace para usá-los de maneira consistente. Depois que os grupos são configurados, um administrador só precisa gerenciar a associação nos grupos de segurança.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Etapa 1: configurar grupos de segurança com nomes que seguem este padrão

1. Criar um grupo de segurança chamado `Synapse_WORKSPACENAME_Users`
2. Criar um grupo de segurança chamado `Synapse_WORKSPACENAME_Admins`
3. Adicionado `Synapse_WORKSPACENAME_Admins` a `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Saiba como criar um grupo de segurança de rede [neste artigo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Saiba como adicionar um grupo de segurança com base em outro grupo de segurança [neste artigo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME – Você deve substituir essa parte pelo nome real do workspace.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Etapa 2: preparar a conta padrão do ADLS Gen2

Depois de provisionar o workspace, você precisará escolher uma conta do [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) e um contêiner para o sistema de arquivos para uso pelo workspace.

1. Abra o [portal do Azure](https://portal.azure.com)
2. Navegar para a conta do Azure Data Lake Storage Gen2
3. Navegue até o contêiner (sistema de arquivos) escolhido para o workspace do Azure Synapse
4. Selecione **Controle de Acesso (IAM)**
5. Atribua as seguintes funções:
   1. Função **Leitor** para: `Synapse_WORKSPACENAME_Users`
   2. Função **Proprietário de Dados do Blob de Armazenamento** para: `Synapse_WORKSPACENAME_Admins`
   3. Função **Colaborador de Dados do Blob de Armazenamento** para: `Synapse_WORKSPACENAME_Users`
   4. Função **Proprietário de Dados do Blob de Armazenamento** para: `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME – Você deve substituir essa parte pelo nome real do workspace.

### <a name="step-3-configure-the-workspace-admin-list"></a>Etapa 3: configurar a lista de administradores do workspace

1. Acesse a [**interface do usuário da Web do Azure Synapse**](https://web.azuresynapse.net)
2. Acesse **Gerenciar**  > **Segurança** > **Controle de acesso**
3. Selecione **Adicionar administrador** e escolha `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Etapa 4: configurar o acesso de administrador do SQL para o workspace

1. Vá para o [Portal do Azure](https://portal.azure.com)
2. Navegue até o workspace
3. Acesse **Configurações** > **Administrador do Active Directory**
4. Selecione **Definir administrador**
5. Selecione `Synapse_WORKSPACENAME_Admins`
6. Escolha **Selecionar**
7. Selecione **Salvar**

> [!NOTE]
> WORKSPACENAME – Você deve substituir essa parte pelo nome real do workspace.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Etapa 5: adicionar e remover usuários e administradores em grupos de segurança

1. Adicionar usuários que precisam de acesso administrativo ao `Synapse_WORKSPACENAME_Admins`
2. Adicionar todos os outros usuários ao `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Saiba como adicionar um usuário como membro em um grupo de segurança [neste artigo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACENAME – Você deve substituir essa parte pelo nome real do workspace.

## <a name="access-control-to-data"></a>Controle de acesso aos dados

O controle de acesso aos dados subjacentes é dividido em três partes:

- Acesso ao plano de dados à conta de armazenamento (já configurado acima na Etapa 2)
- Acesso do plano de dados aos Bancos de Dados SQL (para os pools de SQL dedicados e o pool de SQL sem servidor)
- Como criar uma credencial para bancos de dados do pool de SQL sem servidor na conta de armazenamento

## <a name="access-control-to-sql-databases"></a>Controle de acesso aos bancos de dados SQL

> [!TIP]
> As etapas a seguir precisam ser executadas para **cada** banco de dados SQL a fim de conceder acesso de usuário a todos os bancos de dados SQL, exceto na seção [permissão de nível de servidor](#server-level-permission) em que você pode atribuir a um usuário a função de sysadmin.

### <a name="serverless-sql-pool"></a>Pool de SQL sem servidor

Nesta seção, você pode encontrar exemplos de como conceder a um usuário uma permissão para um banco de dados específico ou permissões totais de servidor.

#### <a name="database-level-permission"></a>Permissão no nível do banco de dados

Para permitir acesso a um usuário a um banco de dados do pool de SQL sem servidor **individual** do SQL sob demanda, siga as etapas deste exemplo:

1. Criar um LOGON

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar um USUÁRIO

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Adicionar o USUÁRIO aos membros da função especificada

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Substitua o alias pelo alias do usuário que você gostaria de conceder acesso e o domínio com o domínio da empresa que você está usando.

#### <a name="server-level-permission"></a>Permissões no nível do servidor

Para permitir acesso completo a um usuário a **todos** os banco de dados do pool de SQL sem servidor, execute a etapa deste exemplo:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Pool de SQL dedicado

Para permitir acesso a um usuário a um banco de dados SQL **individual** , siga estas etapas:

1. Crie o usuário no banco de dados executando o seguinte comando, tendo como destino o banco de dados desejado no seletor de contexto (selecione a lista suspensa para escolher os bancos de dados):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Conceda ao usuário uma função para acessar o banco de dados:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* poderão funcionar para permissões de leitura/gravação se a concessão da permissão *db_owner* não for desejada.
> Para que um usuário do Spark faça leituras e gravações diretamente no Spark de maneira bidirecional em um pool de SQL dedicado, a permissão *db_owner* é necessária.

Depois de criar os usuários, verifique se o pool de SQL sem servidor pode consultar a conta de armazenamento.

## <a name="access-control-to-workspace-pipeline-runs"></a>Controle de acesso às execuções de pipeline do workspace

### <a name="workspace-managed-identity"></a>Identidade gerenciada pelo workspace

> [!IMPORTANT]
> Para executar com êxito os pipelines que incluem conjuntos de dados ou atividades que referenciam um pool de SQL dedicado, a identidade do workspace precisa receber acesso diretamente ao pool de SQL.

Execute os seguintes comandos em cada pool de SQL dedicado para permitir que a identidade gerenciada do workspace execute pipelines no banco de dados do pool de SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Essa permissão pode ser removida pela execução do seguinte script no mesmo pool de SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da identidade gerenciada pelo workspace do Synapse, confira a [Identidade gerenciada pelo workspace do Azure Synapse](../security/synapse-workspace-managed-identity.md). Para saber mais sobre as entidades de banco de dados, confira [Entidades de segurança](https://msdn.microsoft.com/library/ms181127.aspx). Encontre mais informações sobre as funções de banco de dados no artigo [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
