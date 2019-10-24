---
title: Saiba como proteger o acesso aos dados no Azure Cosmos DB
description: Saiba mais sobre os conceitos do controle de acesso no Azure Cosmos DB, incluindo chaves mestras, chaves somente leitura, usuários e permissões.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 7e732f1d35097730d4468b43a2d9804fe7a18514
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753168"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Proteger o acesso aos dados no Azure Cosmos DB

Este artigo fornece uma visão geral de como proteger o acesso aos dados armazenados no [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

O Azure Cosmos DB usa dois tipos de chaves para autenticar usuários e fornecer acesso aos seus dados e recursos. 

|Tipo de chave|Implante|
|---|---|
|[Chaves mestras](#master-keys) |Usadas para os recursos administrativos: contas de bancos de dados, bancos de dados, usuários e permissões|
|[Tokens de recurso](#resource-tokens)|Usado para recursos de aplicativo: contêineres, documentos, anexos, procedimentos armazenados, gatilhos e UDFs|

<a id="master-keys"></a>

## <a name="master-keys"></a>Chaves mestras 

As chaves mestras fornecem acesso a todos os recursos administrativos da conta do banco de dados. Chaves mestras:  
- Fornecem acesso a contas, a bancos de dados, a usuários e a permissões. 
- Não pode ser usado para fornecer acesso granular a contêineres e documentos.
- São criadas durante a criação de uma conta.
- Podem ser geradas novamente a qualquer momento.

Cada conta é formada por duas Chaves mestras: uma chave primária e uma chave secundária. A finalidade das chaves duplas é para que você possa gerar novamente, ou reverter as chaves, fornecendo acesso contínuo à sua conta e dados. 

Além das duas chaves mestras da conta do Cosmos DB, há duas chaves somente leitura. Essas chaves somente leitura só permitem operações de leitura na conta. As chaves somente leitura não fornecem acesso a recursos com permissões de leitura.

As chaves mestras primária, secundária, somente leitura e de leitura-gravação podem ser recuperadas e geradas novamente usando o Portal do Azure. Para obter instruções, veja [Exibir, copiar e gerar novamente as chaves de acesso](manage-with-cli.md#regenerate-account-key).

![Controle de acesso (IAM) no Portal do Azure - demonstrando a segurança do banco de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

O processo de girar a chave mestra é simples. Navegue até o Portal do Azure para recuperar a chave secundária, depois substitua a chave primária pela chave secundária em seu aplicativo e gire a chave primária no Portal do Azure.

![Rotação de chave mestra no Portal do Azure – demonstrando a segurança do banco de dados NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemplo de código para uso de uma chave mestra

O exemplo de código a seguir ilustra como usar o ponto de extremidade e a chave mestra de uma conta do Cosmos DB para criar uma instância de um DocumentClient e criar um banco de dados. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Tokens de recurso

Os tokens de recurso fornecem acesso aos recursos do aplicativo em um banco de dados. Tokens de recurso:
- Fornece acesso a contêineres, chaves de partição, documentos, anexos, procedimentos armazenados, gatilhos e UDFs específicos.
- São criados quando um [usuário](#users) recebe [permissões](#permissions) para um recurso específico.
- São recriados quando um recurso de permissão recebe uma ação de uma chamada POST, GET ou PUT.
- Use um token de recurso de hash construído especificamente para o usuário, o recurso e a permissão.
- São associados a um período de validade personalizável. O intervalo de tempo válido padrão é de uma hora. O tempo de vida do token, no entanto, pode ser especificado explicitamente, até o máximo de cinco horas.
- Fornecem uma alternativa segura para o fornecimento da chave mestra. 
- Permitem aos clientes ler, gravar e excluir recursos da conta do Cosmos DB de acordo com as permissões que receberam.

Você pode usar um token de recurso (criando usuários e permissões do Cosmos DB) quando desejar fornecer acesso aos recursos de sua conta do Cosmos DB a um cliente que não é confiável para receber a chave mestra.  

Os tokens de recurso do Cosmos DB fornecem uma alternativa segura que permite aos clientes ler, gravar e excluir recursos de sua conta do Cosmos DB de acordo com as permissões que você concedeu e sem a necessidade de uma chave mestra ou somente leitura.

Este é um padrão de design típico no qual tokens de recurso podem ser solicitados, gerados e fornecidos aos clientes:

1. Um serviço de camada intermediária é configurado para atender a um aplicativo móvel de compartilhamento de fotos do usuário. 
2. O serviço de camada intermediária tem a chave mestra da conta do Cosmos DB.
3. O aplicativo de fotos é instalado em dispositivos móveis de usuários finais. 
4. No logon, o aplicativo de fotos estabelece a identidade do usuário com o serviço de camada intermediária. Esse mecanismo de estabelecimento de identidade depende apenas do aplicativo.
5. Depois que a identidade é estabelecida, o serviço de camada intermediária solicita permissões com base na identidade.
6. O serviço de camada intermediária envia um token de recurso de volta para o aplicativo móvel.
7. O aplicativo de telefone pode continuar usando o token de recurso para acessar diretamente recursos do Cosmos DB com as permissões definidas pelo token de recurso e no intervalo permitido por ele. 
8. Quando o token de recurso expira, as solicitações seguintes recebem uma exceção 401 de não autorizado.  Nesse ponto, o aplicativo móvel restabelece a identidade e solicita um novo token de recurso.

    ![Fluxo de trabalho dos tokens de recurso do Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

A geração e o gerenciamento do token de recurso são manipulados pelas bibliotecas de cliente nativas do Cosmos DB; no entanto, se você usar a REST, deverá construir os cabeçalhos de solicitação/autenticação. Para obter mais informações sobre como criar cabeçalhos de autenticação para a REST, consulte [Controle de Acesso em recursos do Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) ou o [código-fonte de nossos SDKs](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Para obter um exemplo de um serviço de camada intermediária usado para gerar, ou tokens de recurso do agente, confira o [aplicativo ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Usuários
Cosmos DB usuários estão associados a um banco de dados Cosmos.  Cada banco de dados pode conter nenhum ou mais usuários do Cosmos DB.  O exemplo de código a seguir mostra como criar um recurso de usuário do Cosmos DB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Cada usuário do Cosmos DB tem uma propriedade PermissionsLink que pode ser usada para recuperar a lista de [permissões](#permissions) associadas ao usuário.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Permissões
Um recurso de permissão do Cosmos DB é associado a um usuário do Cosmos DB.  Cada usuário pode conter nenhuma ou mais permissões do Cosmos DB.  Um recurso de permissão fornece acesso a um token de segurança de que o usuário precisa ao tentar acessar um recurso de aplicativo específico.
Há dois níveis de acesso disponíveis que podem ser fornecidos por um recurso de permissão:

* Tudo: o usuário tem permissão total com relação ao recurso.
* Leitura: O usuário pode apenas ler o conteúdo do recurso, mas não pode executar operações de gravação, atualização ou exclusão no recurso.

> [!NOTE]
> Para executar os procedimentos armazenados do Cosmos DB, o usuário precisa ter a permissão Tudo no contêiner no qual o procedimento armazenado será executado.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemplo de código para criar permissão

O exemplo de código a seguir mostra como criar um recurso de permissão, ler o token de recurso do recurso de permissão e associar as permissões ao [usuário](#users) criado anteriormente.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Se você tiver especificado uma chave de partição para sua coleção, depois a permissão para coleta, os recursos de documento e anexo também deverão incluir o ResourcePartitionKey além do ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemplo de código para permissões de leitura para usuário

Para obter facilmente todos os recursos de permissão associados a determinado usuário, o Cosmos DB disponibiliza um feed de permissões para cada objeto de usuário.  O snippet de código a seguir mostra como recuperar a permissão associada ao usuário criado acima, construir uma lista de permissões e instanciar um novo DocumentClient em nome do usuário.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Adicionar usuários e atribuir funções

Para adicionar o acesso de leitor de conta do Azure Cosmos DB à sua conta de usuário, peça ao proprietário de uma assinatura que execute as seguintes etapas no portal do Azure.

1. Abra o portal do Azure e selecione sua conta do Azure Cosmos DB.
2. Clique na guia **Controle de acesso (IAM)** e, em seguida, clique em **+ Adicionar atribuição de função**.
3. No painel **Adicionar atribuição de função**, na caixa **Função**, selecione **Função de leitor de conta do Cosmos DB**.
4. Na caixa **Atribuir acesso à caixa**, selecione **Usuário, grupo ou aplicativo do Microsoft Azure Active Directory**.
5. Selecione o usuário, o grupo ou o aplicativo no diretório ao qual você deseja conceder acesso.  Você pode pesquisar o diretório por nome para exibição, endereço de email ou identificadores de objeto.
    O usuário, grupo ou aplicativo selecionado aparece na lista de membros selecionados.
6. Clique em **Save** (Salvar).

A entidade agora poderá ler recursos do Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Excluir ou exportar dados do usuário
O Azure Cosmos DB permite que você pesquise, selecione, modifique e exclua todos os dados pessoais localizados no banco de dados ou nas coleções. O Azure Cosmos DB fornece APIs para localizar e excluir dados pessoais, no entanto, é sua responsabilidade usar as APIs e definir a lógica necessária para apagar os dados pessoais. Cada API multimodelo (SQL, MongoDB, Gremlin, Cassandra, tabela) fornece SDKs de linguagens diferentes que contêm métodos para pesquisar e excluir dados pessoais. Você também pode habilitar o recurso [TTL (vida útil)](time-to-live.md) para excluir os dados automaticamente após um período especificado, sem resultar em nenhum custo adicional.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximos passos
* Para saber mais sobre a segurança do banco de dados Cosmos, consulte [Cosmos DB: segurança do banco de dados](database-security.md).
* Para saber como construir tokens de autorização do Azure Cosmos DB, consulte [Controle de Acesso em recursos do Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources).
