---
title: Filtros de segurança para cortar os resultados usando Active Directory
titleSuffix: Azure Cognitive Search
description: Controle de acesso no Azure Pesquisa Cognitiva conteúdo usando as identidades do AAD (filtros de segurança e Azure Active Directory).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "72794315"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtros de segurança para aparar os resultados de Pesquisa Cognitiva do Azure usando identidades Active Directory

Este artigo demonstra como usar as identidades de segurança do AAD (Azure Active Directory) junto com filtros no Azure Pesquisa Cognitiva para cortar os resultados da pesquisa com base na associação do grupo de usuários.

Este artigo aborda as seguintes tarefas:
> [!div class="checklist"]
> - Criar usuários e grupos do AAD
> - Associar o usuário ao grupo que você criou
> - Cache dos novos grupos
> - Indexar documentos com grupos associados
> - Emitir uma solicitação de pesquisa com filtro de identificadores de grupo
> 
> [!NOTE]
> Os snippets de código de exemplo neste artigo são escritos em C#. Você pode encontrar o código-fonte completo [no GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Pré-requisitos

Seu índice no Azure Pesquisa Cognitiva deve ter um [campo de segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo com acesso de leitura ao documento. Esse caso de uso pressupõe uma correspondência individual entre um item protegível (como um aplicativo de admissão de faculdade de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (equipe de admissão).

Você deve ter permissões de administrador do AAD, necessárias neste passo a passo para a criação de usuários, grupos e associações no AAD.

O aplicativo também deve ser registrado com o AAD, conforme descrito no procedimento a seguir.

### <a name="register-your-application-with-aad"></a>Registre seu aplicativo com o AAD

Essa etapa integra o seu aplicativo com o AAD com a finalidade de aceitação de entradas de usuário e contas de grupo. Se você não for um administrador do AAD na sua organização, você pode precisar [criar um novo locatário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) para executar as etapas a seguir.

1. Vá para o [**portal**](https://apps.dev.microsoft.com) >  de registro de aplicativos > **aplicativo convergido****Adicionar um aplicativo**.
2. Insira o nome para o seu aplicativo e clique em **Criar**. 
3. Selecione seu aplicativo recentemente registrado na página Meus aplicativos.
4. Na página de registro do aplicativo > **plataformas** > **Adicionar plataforma**, escolha **API Web**.
5. Ainda na página de registro do aplicativo, vá para > **Microsoft Graph permissões** > **Adicionar**.
6. Em Selecionar Permissões, adicione as seguintes permissões delegadas e, em seguida, clique em **Ok**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

O Microsoft Graph fornece uma API que permite o acesso programático ao AAD por meio de uma REST API. O exemplo de código para este passo a passo usa as permissões para chamar a API do Microsoft Graph para a criação de grupos, usuários e associações. As APIs também são usadas para o cache de identificadores de grupo para uma filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar usuários e grupos

Se você estiver adicionando pesquisa a um aplicativo estabelecido, você pode ter um usuário existente e identificadores de grupo no AAD. Nesse caso, você pode ignorar as próximas três etapas. 

No entanto, se você não tiver os usuários existentes, você pode usar as APIs do Microsoft Graph para criar entidades de segurança. Os trechos de código a seguir demonstram como gerar identificadores, que se tornam valores de dados para o campo de segurança no índice de Pesquisa Cognitiva do Azure. Em nosso hipotético aplicativo de admissão de faculdade, isso seria os identificadores de segurança para a equipe de admissão.

A associação de grupo e de usuário pode ser muito flexível, especialmente em organizações de grandes porte. O código que cria as identidades de usuário e de grupo deve ser executado com frequência suficiente para acompanhar as alterações na associação da organização. Da mesma forma, seu índice de Pesquisa Cognitiva do Azure requer uma agenda de atualização semelhante para refletir o status atual de usuários e recursos permitidos.

### <a name="step-1-create-aad-group"></a>Etapa 1: Criar [Grupo AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Etapa 2: Criar [Usuário AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Etapa 3: Associar o usuário e o grupo
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Etapa 4: Armazenar em Cache os identificadores de grupos
Opcionalmente, para reduzir a latência de rede, você pode armazenar em cache as associações de grupo de usuários para que quando uma solicitação de pesquisa for emitida, os grupos sejam retornados do cache, economizando uma ida e volta ao AAD. Você pode usar a [API do lote do AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) para enviar uma única solicitação HTTP com vários usuários e criar o cache.

O Microsoft Graph foi projetado para lidar com um grande volume de solicitações. Caso haja um número excessivo de solicitações, o Microsoft Graph apresenta uma falha na solicitação com o código de status HTTP 429. Para saber mais, veja [Limitação de mecanismo do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento do índice com seus grupos permitidos

As operações de consulta no Azure Pesquisa Cognitiva são executadas em um índice de Pesquisa Cognitiva do Azure. Nesta etapa, uma operação de indexação importa dados pesquisáveis em um índice, incluindo os identificadores usados como filtros de segurança. 

O Azure Pesquisa Cognitiva não autentica identidades de usuário ou fornece lógica para estabelecer qual conteúdo um usuário tem permissão para exibir. O caso de uso para as restrições de segurança pressupõe que você forneça a associação entre um documento confidencial e o identificador do grupo que tem acesso a esse documento, importado intacto a um índice de pesquisa. 

No exemplo hipotético, o corpo da solicitação PUT em um índice de Pesquisa Cognitiva do Azure incluiria uma dissertação de faculdade ou transcrição do candidato junto com o identificador de grupo com permissão para exibir esse conteúdo. 

No exemplo genérico usado na amostra de código para este passo a passo, a ação de índice será semelhante ao seguinte:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Emitir uma solicitação de pesquisa

Para fins de restrições de segurança, os valores no seu campo de segurança no índice são valores estáticos usados para incluir ou excluir documentos nos resultados da pesquisa. Por exemplo, se o identificador de grupo para admissões for "A11B22C33D44-E55F66G77-H88I99JKK", todos os documentos em um índice de Pesquisa Cognitiva do Azure com esse identificador no arquivamento de segurança serão incluídos (ou excluídos) nos resultados da pesquisa enviados de volta ao solicitante.

Para filtrar os documentos retornados nos resultados da pesquisa com base nos grupos de usuários que emitirem a solicitação, analise as etapas a seguir.

### <a name="step-1-retrieve-users-group-identifiers"></a>Etapa 1: Recuperar os identificadores de grupo do usuário

Se os grupos do usuário já não estavam em cache ou se o cache tiver expirado, emita a solicitação ade [grupos](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Etapa 2: Criar a solicitação de pesquisa

Supondo que você tenha associação de grupos do usuário, você pode emitir a solicitação de pesquisa com os valores de filtro apropriados.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Etapa 3: Lidar com os resultados

A resposta inclui uma lista filtrada de documentos, que consiste naqueles que o usuário tem permissão para visualizar. Dependendo de como você criar a página de resultados da pesquisa, você talvez queira incluir dicas visuais para refletir o conjunto de resultados filtrados.

## <a name="conclusion"></a>Conclusão

Neste tutorial, você aprendeu técnicas para usar entradas do AAD para filtrar documentos nos resultados de Pesquisa Cognitiva do Azure, aparando os resultados de documentos que não correspondem ao filtro fornecido na solicitação.

## <a name="see-also"></a>Confira também

+ [Controle de acesso baseado em identidade usando filtros de Pesquisa Cognitiva do Azure](search-security-trimming-for-azure-search.md)
+ [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
+ [Segurança de dados e controle de acesso nas operações de Pesquisa Cognitiva do Azure](search-security-overview.md)
