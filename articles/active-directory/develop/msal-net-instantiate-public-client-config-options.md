---
title: Instanciar um aplicativo público ao cliente (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a instanciar um aplicativo cliente público com opções de configuração usando a Biblioteca de Autenticação Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083603"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instanciar um aplicativo cliente público com opções de configuração usando MSAL.NET

Este artigo descreve como instanciar um [aplicativo cliente público](msal-client-applications.md) usando a Biblioteca de Autenticação Microsoft para .NET (MSAL.NET).  O aplicativo é instanciado com opções de configuração definidas em um arquivo de configurações.

Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo](quickstart-register-app.md) para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft. Após o cadastro, você pode precisar das seguintes informações (que podem ser encontradas no portal azure):

- O ID do cliente (uma string representando um GUID)
- O URL do provedor de identidade (chamado de instância) e o público de login do seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se você estiver escrevendo uma linha de aplicativo de negócios exclusivamente para sua organização (também chamado de aplicativo de inquilino único).
- Para aplicativos web e, às vezes, para aplicativos públicos de clientes (em particular quando seu aplicativo precisa usar uma corretora), você também terá definido o redirectUri onde o provedor de identidade entrará em contato com seu aplicativo com os tokens de segurança.


Um aplicativo de console .NET Core pode ter o seguinte arquivo de configuração *appsettings.json:*

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

O código a seguir lê este arquivo usando a estrutura de configuração .NET:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

O código a seguir cria seu aplicativo, usando a configuração do arquivo de configurações:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

