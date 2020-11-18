---
title: Criar, gerenciar e proteger as chaves de API de administração e de consulta
titleSuffix: Azure Cognitive Search
description: Uma API-key controla o acesso ao ponto de extremidade de serviço. Chaves de administração concedem acesso de gravação. As chaves de consulta podem ser criadas para acesso somente leitura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0e209e8114d8f1791a00e87894fa12206edcf34e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700215"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Criar e gerenciar chaves de API para um serviço de Pesquisa Cognitiva do Azure

Todas as solicitações para um serviço de pesquisa precisam de uma somente leitura `api-key` que foi gerada especificamente para o seu serviço. O `api-key` é o único mecanismo para autenticar o acesso ao ponto de extremidade do serviço de pesquisa e deve ser incluído em cada solicitação. 

+ Em [soluções REST](search-get-started-rest.md), a chave de API é normalmente especificada em um cabeçalho de solicitação

+ Em [soluções .net](search-howto-dotnet-sdk.md), uma chave é geralmente especificada como uma definição de configuração e, em seguida, passada como um [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

As chaves são criadas com o serviço de pesquisa durante o provisionamento de serviços. É possível exibir e obter valores de chave no [portal do Azure](https://portal.azure.com).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Página do portal, configurações de recuperação, seção de chaves" border="false":::

## <a name="what-is-an-api-key"></a>O que é uma api-key

Uma chave de api é uma cadeia de caracteres composta de letras e números gerados aleatoriamente. Por meio de [permissões baseadas em função](search-security-rbac.md), você pode excluir ou ler as chaves, mas não pode substituir uma chave por uma senha definida pelo usuário ou usar o Active Directory como a metodologia de autenticação primária para acessar operações de pesquisa. 

Dois tipos de chaves são usados para acessar seu serviço de pesquisa: administrador (leitura-gravação) e consulta (somente leitura).

|Chave|Descrição|Limites|  
|---------|-----------------|------------|  
|Administrador|Concede direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados.<br /><br /> Duas chaves de API de administrador, chamadas de chaves *primária* e *secundária* no portal, são geradas quando o serviço é criado e podem ser regeneradas individualmente sob demanda. Ter duas chaves permite que você substitua uma chave enquanto usa a segunda chave de acesso contínuo para o serviço.<br /><br /> As chaves de administrador são especificadas somente nos cabeçalhos de solicitação HTTP. Não é possível colocar uma api-key de administrador em uma URL.|Máximo de dois por serviço|  
|Consulta|Concede acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.<br /><br /> As chaves de consulta são criadas sob demanda. Você pode criá-las manualmente no portal ou programaticamente por meio da [API REST de gerenciamento](/rest/api/searchmanagement/).<br /><br /> As chaves de consulta podem ser especificadas em um cabeçalho de solicitação HTTP para pesquisa, sugestões ou operação de pesquisa. Como alternativa, você pode passar uma chave de consulta como um parâmetro em uma URL. Dependendo de como seu aplicativo cliente formula a solicitação, pode ser mais fácil passar a chave como um parâmetro de consulta:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 por serviço|  

 Visualmente, não há nenhuma distinção entre as chaves de administrador ou de consulta. As duas chaves são cadeias de caracteres compostas de 32 caracteres alfanuméricos gerados aleatoriamente. Se você perder o controle de qual tipo de chave está especificado em seu aplicativo, poderá [verificar os valores de chave no portal](https://portal.azure.com) ou usar a [API REST](/rest/api/searchmanagement/) para retornar o valor e o tipo de chave.  

> [!NOTE]  
>  Passar dados confidenciais, como um `api-key`, no URI de solicitação, é considerado uma prática de segurança inadequada. Por esse motivo, o Azure Pesquisa Cognitiva aceita apenas uma chave de consulta como uma `api-key` na cadeia de caracteres de consulta, e você deve evitar fazer isso, a menos que o conteúdo do índice esteja disponível publicamente. Em vez disso, recomendamos passar a `api-key` como um cabeçalho de solicitação.  

## <a name="find-existing-keys"></a>Localizar chaves existentes

Você pode obter chaves de acesso no portal ou por meio da [API REST de Gerenciamento](/rest/api/searchmanagement/). Para obter mais informações, consulte [Gerenciar as chaves de API de administrador e consulta](search-security-api-keys.md).

1. Entre no [portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página Visão geral, clique em **configurações**  > **chaves** para exibir as chaves de administrador e consulta.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Página do portal, configurações de exibição, seção chaves" border="false":::

## <a name="create-query-keys"></a>Criar chaves de consulta

As chaves de consulta são usadas para acesso somente leitura a documentos dentro de um índice para operações direcionadas a uma coleção de documentos. Consultas de pesquisa, filtro e sugestão são operações que usam uma chave de consulta. Qualquer operação somente leitura que retorna dados do sistema ou definições de objeto, como uma definição de índice ou um status de indexador, requer uma chave de administração.

Restringir o acesso e as operações em aplicativos cliente é essencial para proteger os ativos de pesquisa em seu serviço. Sempre use uma chave de consulta em vez de uma chave de administração para qualquer consulta proveniente de um aplicativo cliente.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Liste os [serviços de pesquisa](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) para sua assinatura.
3. Selecione o serviço e, na página Visão geral, clique em **configurações**  > **chaves**.
4. Clique em **gerenciar chaves de consulta**.
5. Use a chave de consulta já gerada para seu serviço ou crie até 50 novas chaves de consulta. A chave de consulta padrão não é nomeada, mas chaves de consulta adicionais podem ser nomeadas para capacidade de gerenciamento.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Criar ou usar uma chave de consulta" border="false":::

> [!Note]
> Um exemplo de código que mostra o uso da chave de consulta pode ser encontrado em [consultar um índice de pesquisa cognitiva do Azure em C#](./search-get-started-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

Duas chaves de administração são criadas para cada serviço para que você possa girar uma chave primária usando a chave secundária para continuidade dos negócios.

1. Na página **Settings**  > **chaves** de configurações, copie a chave secundária.
2. Para todos os aplicativos, atualize as configurações de chave de API para usar a chave secundária.
3. Regenere a chave primária.
4. Atualize todos os aplicativos para usar a nova chave primária.

Se você regenerar inadvertidamente as duas chaves ao mesmo tempo, todas as solicitações de cliente que usam essas chaves falharão com HTTP 403 Proibido. No entanto, o conteúdo não é excluído e você não está bloqueado permanentemente. 

Você ainda pode acessar o serviço por meio do portal ou da camada de gerenciamento ([API REST](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)ou Azure Resource Manager). As funções de gerenciamento estão funcionando por meio de uma ID de assinatura que não é uma chave de API de serviço e, assim, ainda ficam disponíveis mesmo que suas chaves de API não sejam. 

Depois de criar novas chaves por meio do portal ou da camada de gerenciamento, o acesso é restaurado para seu conteúdo (índices, indexadores, fontes de dados, mapas de sinônimos) quando você tem as novas chaves e fornece essas chaves em solicitações.

## <a name="secure-api-keys"></a>Proteger api-keys
A chave de segurança é protegida restringindo o acesso por meio do portal ou interfaces do Gerenciador de Recursos (PowerShell ou interface de linha de comando). Conforme observado, os administradores de assinatura podem exibir e gerar novamente todas as chaves de api. Como precaução, revise as atribuições de função para entender quem tem acesso às chaves admin.

+ No painel de serviços, clique em **Controle de Acesso (IAM)** e, em seguida, na guia **Atribuições de função** para exibir as atribuições de função do serviço.

Membros das funções a seguir podem exibir e regenerar chaves: Proprietário, Colaborador, [Colaboradores do Serviço de Pesquisa](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> Para o acesso baseado em identidade sobre os resultados da pesquisa, é possível criar filtros de segurança para cortar resultados por identidade, removendo documentos para os quais o solicitante não deve ter acesso. Para obter mais informações, consulte [Filtros de segurança](search-security-trimming-for-azure-search.md) e [Proteger com Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Veja também

+ [Controle de acesso baseado em função do Azure no Azure Pesquisa Cognitiva](search-security-rbac.md)
+ [Gerenciar usando o PowerShell](search-manage-powershell.md) 
+ [Artigo Desempenho e otimização](search-performance-optimization.md)