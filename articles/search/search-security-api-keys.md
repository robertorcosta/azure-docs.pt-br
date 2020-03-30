---
title: Criar, gerenciar e proteger admin e consultar chaves de api
titleSuffix: Azure Cognitive Search
description: Uma chave api controla o acesso ao ponto final do serviço. Chaves de administração concedem acesso de gravação. As chaves de consulta podem ser criadas para acesso somente leitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794382"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Crie e gerencie chaves de api para um serviço de pesquisa cognitiva do Azure

Todas as solicitações para um serviço de pesquisa precisam de uma api-key somente leitura que tenha sido gerada especificamente para o serviço. A api-key é o único mecanismo para autenticar o acesso ao ponto de extremidade de serviço de pesquisa e deve ser incluída em todas as solicitações. Em [soluções REST](search-get-started-postman.md), a api-key normalmente é especificada em um cabeçalho de solicitação. Em [soluções .NET](search-howto-dotnet-sdk.md#core-scenarios), uma chave frequentemente é especificada como uma definição de configuração e, em seguida, transmitida como [Credenciais](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chave de administração) ou [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chave de consulta) no [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

As chaves são criadas com o serviço de pesquisa durante o provisionamento de serviços. É possível exibir e obter valores de chave no [portal do Azure](https://portal.azure.com).

![Página do portal, Configurações, seção Chaves](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>O que é uma api-key

Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. Por meio de [permissões baseadas em função](search-security-rbac.md), você pode excluir ou ler as chaves, mas não pode substituir uma chave por uma senha definida pelo usuário ou usar o Active Directory como a metodologia de autenticação primária para acessar operações de pesquisa. 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa: administrador (leitura-gravação) e consulta (somente leitura).

|Chave|Descrição|limites|  
|---------|-----------------|------------|  
|Admin|Concede direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados.<br /><br /> Duas chaves de API de administrador, chamadas de chaves *primária* e *secundária* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você substitua uma chave enquanto usa a segunda chave de acesso contínuo para o serviço.<br /><br /> As chaves de administrador são especificadas somente nos cabeçalhos de solicitação HTTP. Não é possível colocar uma api-key de administrador em uma URL.|Máximo de dois por serviço|  
|Consulta|Concede acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda. Você pode criá-las manualmente no portal ou programaticamente por meio da [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestões ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como seu aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre as chaves de administrador ou de consulta. As duas chaves são cadeias de caracteres compostas de 32 caracteres alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave está especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com) ou usar a [API REST](https://docs.microsoft.com/rest/api/searchmanagement/) para retornar o valor e o tipo de chave.  

> [!NOTE]  
>  Passar dados confidenciais, como um `api-key`, no URI de solicitação, é considerado uma prática de segurança inadequada. Por essa razão, o Azure Cognitive Search só `api-key` aceita uma chave de consulta como uma na seqüência de consultas, e você deve evitar fazê-lo a menos que o conteúdo do seu índice esteja disponível publicamente. Em vez disso, recomendamos passar a `api-key` como um cabeçalho de solicitação.  

## <a name="find-existing-keys"></a>Encontre as chaves existentes

Você pode obter chaves de acesso no portal ou por meio da [API REST de Gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/). Para obter mais informações, consulte [Gerenciar as chaves de API de administrador e consulta](search-security-api-keys.md).

1. Faça login no [portal Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e na página Visão geral, clique em **Teclas** >**de configuração** para exibir as teclas de admin e consulta.

   ![Página do portal, Configurações, seção Chaves](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são usadas para acesso somente à leitura de documentos dentro de um índice para operações direcionadas a uma coleta de documentos. Consultas de pesquisa, filtro e sugestão são todas as operações que levam uma chave de consulta. Qualquer operação somente leitura que retorne os dados do sistema ou definições de objeto, como uma definição de índice ou status de indexador, requer uma chave de administrador.

Restringir o acesso e as operações em aplicativos clientes é essencial para proteger os ativos de pesquisa em seu serviço. Use sempre uma chave de consulta em vez de uma chave de admin para qualquer consulta originária de um aplicativo cliente.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e na página Visão geral, clique em **Teclas** >**de configuração**.
4. Clique **em Gerenciar as teclas de consulta**.
5. Use a chave de consulta já gerada para o seu serviço ou crie até 50 novas chaves de consulta. A chave de consulta padrão não é nomeada, mas as chaves de consulta adicionais podem ser nomeadas para capacidade de gerenciamento.

   ![Criar ou usar uma chave de consulta](media/search-security-overview/create-query-key.png) 

> [!Note]
> Um exemplo de código mostrando o uso da chave de consulta pode ser encontrado na [consulta de um índice de pesquisa cognitiva do Azure em C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Duas teclas de administração são criadas para cada serviço para que você possa girar uma chave primária, usando a chave secundária para a continuidade de negócios.

1. Na página**Teclas de** **configurações,** >copie a tecla secundária.
2. Para todos os aplicativos, atualize as configurações de chave de API para usar a chave secundária.
3. Regenere a chave primária.
4. Atualize todos os aplicativos para usar a nova chave primária.

Se você regenerar inadvertidamente ambas as chaves ao mesmo tempo, todas as solicitações de clientes usando essas chaves falharão com HTTP 403 Proibido. No entanto, o conteúdo não é excluído e você não está bloqueado permanentemente. 

Você ainda pode acessar o serviço através do portal ou da camada de gerenciamento[(REST API,](https://docs.microsoft.com/rest/api/searchmanagement/) [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)ou Azure Resource Manager). As funções de gerenciamento são operacionais através de um ID de assinatura e não de um api-key de serviço e, portanto, ainda estão disponíveis mesmo que suas teclas de api não estejam. 

Depois de criar novas chaves via portal ou camada de gerenciamento, o acesso é restaurado ao seu conteúdo (índices, indexadores, fontes de dados, mapas de sinônimos) uma vez que você tiver as novas chaves e fornecer essas chaves nas solicitações.

## <a name="secure-api-keys"></a>Proteger api-keys
A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Conforme observado, os administradores de assinatura podem exibir e gerar novamente todas as chaves de api. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

+ No painel de serviços, clique em **Controle de Acesso (IAM)** e, em seguida, na guia **Atribuições de função** para exibir as atribuições de função do serviço.

Membros das funções a seguir podem exibir e regenerar chaves: Proprietário, Colaborador, [Colaboradores do Serviço de Pesquisa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Confira também

+ [Controle de acesso baseado em função na Pesquisa Cognitiva do Azure](search-security-rbac.md)
+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Artigo Desempenho e otimização](search-performance-optimization.md)