---
title: Como verificar a análise de Synapse do Azure
description: Este guia de instruções descreve os detalhes de como examinar a análise de Synapse do Azure.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: e0a1d8dba9ea284322584de3b4be2ae390d15fdf
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920260"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrar e verificar o Azure Synapse Analytics

Este artigo discute como registrar e verificar uma instância do Azure Synapse Analytics (anteriormente conhecido como SQL DW) no alcance.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

O Azure Synapse Analytics (anteriormente conhecido como SQL DW) dá suporte a verificações completas e incrementais para capturar os metadados e o esquema. As verificações também classificam os dados automaticamente com base em regras de classificação personalizada e do sistema.

### <a name="known-limitations"></a>Limitações conhecidas

O Azure alcance não dá suporte à verificação de [exibições](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) na análise de Synapse do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Antes de registrar as fontes de dados, crie uma conta do Azure alcance. Para obter mais informações sobre como criar uma conta do alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
- Você precisa ser um administrador de fonte de dados do Azure alcance
- Acesso à rede entre a conta do alcance e o Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Configurando a autenticação para uma verificação

Há três maneiras de configurar a autenticação para o armazenamento de BLOBs do Azure:

- Identidade Gerenciada
- Autenticação do SQL
- Entidade de Serviço

    > [!Note]
    > Somente o logon da entidade de segurança no nível do servidor (criado pelo processo de provisionamento) ou membros da função de banco de dados `loginmanager` no banco de dados mestre podem criar novos logons. Leva cerca de 15 minutos após a concessão da permissão, a conta alcance deve ter as permissões apropriadas para poder verificar os recursos.

### <a name="managed-identity-recommended"></a>Identidade gerenciada (recomendado) 
   
Sua conta do alcance tem sua própria identidade gerenciada, que é basicamente seu nome de alcance quando você a criou. Você deve criar um usuário do Azure AD no Azure Synapse Analytics (anteriormente conhecido como SQL DW) com o nome de identidade gerenciado exato do alcance seguindo os pré-requisitos e o tutorial sobre como [criar usuários do Azure ad usando aplicativos do Azure ad](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial).

Exemplo de sintaxe SQL para criar usuário e conceder permissão:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

A autenticação deve ter permissão para obter metadados para o banco de dados, esquemas e tabelas. Ele também deve ser capaz de consultar as tabelas para obter um exemplo de classificação. A recomendação é atribuir `db_owner` permissão à identidade.

### <a name="service-principal"></a>Entidade de Serviço

Para usar a autenticação de entidade de serviço para verificações, você pode usar uma existente ou criar uma nova. 

> [!Note]
> Se você precisar criar uma nova entidade de serviço, siga estas etapas:
> 1. Navegue até o [Portal do Azure](https://portal.azure.com).
> 1. Selecione **Azure Active Directory** no menu do lado esquerdo.
> 1. Selecione **Registros do Aplicativo**.
> 1. Selecione **+Novo registro de aplicativo**.
> 1. Insira um nome para o **aplicativo** (o nome da entidade de serviço).
> 1. Selecione **contas somente neste diretório organizacional**.
> 1. Para URI de redirecionamento, selecione **Web** e insira qualquer URL desejada; Ele não precisa ser real nem funcionar.
> 1. Em seguida, selecione **Registrar**.

É necessário obter a ID e o segredo do aplicativo da entidade de serviço:

1. Navegue até a entidade de serviço na [portal do Azure](https://portal.azure.com)
1. Copie os valores da **ID do aplicativo (cliente)** da **visão geral** e do **segredo do cliente** dos **certificados & segredos**.
1. Navegue até o Key Vault
1. Selecione **configurações > segredos**
1. Selecione **+ gerar/importar** e insira o **nome** de sua escolha e **valor** como o **segredo do cliente** de sua entidade de serviço
1. Selecione **criar** para concluir
1. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a entidade de serviço para configurar sua verificação 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Concedendo acesso à entidade de serviço ao Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Além disso, você também deve criar um usuário do Azure AD no Azure Synapse Analytics seguindo os pré-requisitos e o tutorial sobre como [criar usuários do Azure ad usando aplicativos do Azure ad](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial). Exemplo de sintaxe SQL para criar usuário e conceder permissão:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> O alcance precisará da **ID do aplicativo (cliente)** e do **segredo do cliente** para verificar.

### <a name="sql-authentication"></a>Autenticação do SQL

Você pode seguir as instruções em [criar logon](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) para criar um logon para o Azure Synapse Analytics (anteriormente conhecido como SQL DW), se você ainda não tiver um.

Quando o método de autenticação selecionado for **SQL Authentication**, você precisará obter sua senha e armazená-la no cofre de chaves:

1. Obter a senha para seu logon do SQL
1. Navegue até o Key Vault
1. Selecione **configurações > segredos**
1. Selecione **+ gerar/importar** e insira o **nome** e o **valor** como a *senha* para o logon do SQL
1. Selecione **criar** para concluir
1. Se o cofre de chaves ainda não estiver conectado ao alcance, você precisará [criar uma nova conexão de Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Por fim, [crie uma nova credencial](manage-credentials.md#create-a-new-credential) usando a chave para configurar sua verificação

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrar uma instância do Azure Synapse Analytics (anteriormente conhecido como SQL DW)

Para registrar um novo servidor do Azure Synapse Analytics em seu catálogo de dados, faça o seguinte:

1. Navegue até sua conta do alcance
1. Selecionar **fontes** no painel de navegação esquerdo
1. Escolha **Registrar**
1. Em **registrar fontes**, selecione **Azure Synapse Analytics (anteriormente conhecido como SQL DW)**
1. Selecione **Continuar**

Na tela **registrar fontes (Azure Synapse Analytics)** , faça o seguinte:

1. Insira um **nome** no qual a fonte de dados será listada no catálogo.
1. Escolha como você deseja apontar para a conta de armazenamento desejada:
   1. Selecione **da assinatura do Azure**, selecione a assinatura apropriada na caixa suspensa **assinatura do Azure** e o servidor apropriado na caixa suspensa **nome do servidor** .
   1. Ou, você pode selecionar **inserir manualmente** e inserir um **nome de servidor**.
1. **Concluir** para registrar a fonte de dados.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opções de registrar fontes" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Próximas etapas

- [Procurar o catálogo de dados do Azure alcance](how-to-browse-catalog.md)
- [Pesquisar no catálogo de dados do Azure alcance](how-to-search-catalog.md)

