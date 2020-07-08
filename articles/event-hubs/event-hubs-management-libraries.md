---
title: Bibliotecas de gerenciamento - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre a biblioteca que você pode usar para gerenciar namespaces de Hubs de Eventos do Azure e entidades do .NET.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f3129ae5586a3096dda89eea3af21eefd1606f30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312986"
---
# <a name="event-hubs-management-libraries"></a>Bibliotecas de gerenciamento dos Hubs de Eventos

Você pode usar as bibliotecas de gerenciamento de Hubs de eventos podem provisionar dinamicamente entidades e namespaces de Hubs de Eventos do Azure. Essa natureza dinâmica permite implantações e cenários de mensagens complexos, para que seja possível determinar de forma programática quais entidades serão provisionadas. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade com suporte

* Criação, atualização, exclusão de namespace
* Criação de Hubs de eventos, atualização, exclusão
* Criação de grupo de consumidores, atualização, exclusão

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento de Hubs de eventos, você deverá se autenticar com o Azure Active Directory (AAD). AAD exige que você autentique como uma entidade de serviço, que fornece acesso aos recursos do Azure. Para saber mais sobre como criar uma entidade de serviço, veja um dos seguintes artigos:  

* [Use o portal do Azure para criar Active Directory aplicativo e entidade de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais fornecem uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todas usadas para autenticação pelas bibliotecas de gerenciamento. Você deve ter permissões de **Proprietário** para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de Hubs de eventos a seguir, um protocolo comum:

1. Obtenha um token do AAD usando a biblioteca `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Crie o objeto `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Defina os parâmetros `CreateOrUpdate` com os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a chamada.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Próximas etapas
* [Exemplo de gerenciamento do .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referência de Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
