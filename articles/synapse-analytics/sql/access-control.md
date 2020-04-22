---
title: Gerenciar o acesso a workspaces, dados e pipelines
description: Saiba como gerenciar o controle de acesso a workspaces, dados e pipelines em um workspace do Azure Synapse Analytics (versão prévia).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 89d2105ab080309639c4341072c3f5f36608dfce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421100"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gerenciar o acesso a workspaces, dados e pipelines

Saiba como gerenciar o controle de acesso a workspaces, dados e pipelines em um workspace do Azure Synapse Analytics (versão prévia).

> [!NOTE]
> Para GA, o RBAC será mais desenvolvido por meio da introdução de funções RBAC do Azure específicas do Synapse

## <a name="access-control-for-workspace"></a>Controle de acesso ao workspace

Para uma implantação de produção em um workspace do Azure Synapse, sugerimos organizar seu ambiente para facilitar o provisionamento de usuários e administradores.

> [!NOTE]
> A abordagem utilizada aqui é criar vários grupos de segurança e configurar o workspace para usá-los de maneira consistente. Depois que os grupos são configurados, um administrador só precisa gerenciar a associação nos grupos de segurança.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Etapa 1: configurar grupos de segurança com nomes que seguem este padrão

1. Criar um grupo de segurança chamado `Synapse_WORKSPACENAME_Users`
2. Criar um grupo de segurança chamado `Synapse_WORKSPACENAME_Admins`
3. Adicionado `Synapse_WORKSPACENAME_Admins` a `ProjectSynapse_WORKSPACENAME_Users`

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Etapa 2: preparar a conta padrão do ADLS Gen2

Depois de provisionar seu workspace, você precisará escolher uma conta do ADLSGEN2 e um contêiner para uso pelo sistema de arquivos do workspace.

1. Abra o [portal do Azure](https://portal.azure.com)
2. Navegue até a conta do ADLSGEN2
3. Navegue até o contêiner (sistema de arquivos) escolhido para o workspace do Azure Synapse
4. Clique em **Controle de Acesso (IAM)**
5. Atribua as seguintes funções:
   1. Função **Leitor**: `Synapse_WORKSPACENAME_Users`
   2. Função **Proprietário de Dados do Blob de Armazenamento**: `Synapse_WORKSPACENAME_Admins`
   3. Função **Colaborador de Dados do Blob de Armazenamento**: `Synapse_WORKSPACENAME_Users`
   4. Função **Proprietário de Dados do Blob de Armazenamento**: `WORKSPACENAME`
  
### <a name="step-3-configure-the-workspace-admin-list"></a>Etapa 3: configurar a lista de administradores do workspace

1. Acesse a [**interface do usuário da Web do Azure Synapse**](https://web.azuresynapse.net)
2. Acesse **Gerenciar**  > **Segurança** > **Controle de acesso**
3. Clique em **Adicionar Administrador** e escolha `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Etapa 4: configurar o acesso de administrador do SQL para o workspace

1. Vá para o [Portal do Azure](https://portal.azure.com)
2. Navegue até o workspace
3. Acesse **Configurações** > **Administrador do Active Directory**
4. Clique em **Definir Administrador**
5. Selecione `Synapse_WORKSPACENAME_Admins`
6. Clique em **Selecionar**
7. Clique em **Salvar**

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Etapa 5: adicionar e remover usuários e administradores em grupos de segurança

1. Adicionar usuários que precisam de acesso administrativo ao `Synapse_WORKSPACENAME_Admins`
2. Adicionar todos os outros usuários ao `Synapse_WORKSPACENAME_Users`

## <a name="access-control-to-data"></a>Controle de acesso aos dados

O controle de acesso aos dados subjacentes é dividido em três partes:

- Acesso ao plano de dados à conta de armazenamento (já configurado acima na Etapa 2)
- Acesso ao plano de dados aos bancos de dados SQL (para os pools de SQL e o SQL sob demanda)
- Como criar uma credencial para bancos de dados do SQL sob demanda na conta de armazenamento

## <a name="access-control-to-sql-databases"></a>Controle de acesso aos bancos de dados SQL

> [!TIP]
> As etapas abaixo precisam ser executadas para **cada** Banco de Dados SQL, a fim de permitir acesso de usuário a todos os bancos de dados SQL.

### <a name="sql-on-demand"></a>SQL sob demanda

Para permitir acesso a um usuário a um banco de dados **individual** do SQL sob demanda, siga as etapas deste exemplo:

1. Criar um LOGON

    ```sql
    use master
    go
    CREATE LOGIN [John.Thomas@microsoft.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Criar um USUÁRIO

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER john FROM LOGIN [John.Thomas@microsoft.com];
    ```

3. Adicionar o USUÁRIO aos membros da função especificada

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member john -- Type USER name from step 2
    ```

### <a name="sql-pools"></a>Pools de SQL

Para permitir acesso a um usuário a um Banco de Dados SQL **individual**, siga estas etapas:

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
> Para que um usuário do Spark faça leituras e gravações diretamente no Spark de maneira bidirecional em um pool de SQL, a permissão *db_owner* é necessária.

Depois de criar os usuários, verifique se o SQL sob demanda pode consultar a conta de armazenamento:

- Execute o seguinte comando tendo como destino o banco de dados **mestre** do SQL sob demanda:

    ```sql
    CREATE CREDENTIAL [https://<storageaccountname>.dfs.core.windows.net]
    WITH IDENTITY='User Identity';
    ```

## <a name="access-control-to-workspace-pipeline-runs"></a>Controle de acesso às execuções de pipeline do workspace

### <a name="workspace-managed-identity"></a>Identidade gerenciada pelo workspace

> [!IMPORTANT]
> Para executar com êxito os pipelines que incluem conjuntos de dados ou atividades que referenciam um pool de SQL, a identidade do workspace precisa receber acesso diretamente ao pool de SQL.

Execute os seguintes comandos em cada pool de SQL para permitir que a identidade gerenciada do workspace execute pipelines no banco de dados do pool de SQL:

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

Para obter uma visão geral do acesso e do controle no Synapse SQL, confira [Controle de acesso do Synapse SQL](../sql/access-control.md). Para saber mais sobre as entidades de banco de dados, confira [Entidades de segurança](https://msdn.microsoft.com/library/ms181127.aspx). Encontre mais informações sobre as funções de banco de dados no artigo [Funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
