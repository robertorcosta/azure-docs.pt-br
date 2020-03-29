---
title: Gerencie dados de referência em ambientes GA usando C# - Azure Time Series Insights | Microsoft Docs
description: Aprenda a gerenciar dados de referência para o seu ambiente GA criando um aplicativo personalizado escrito em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: cf5f89197798f95dced5bfd8817f1df050297048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961992"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>Gerencie dados de referência ga para um ambiente Azure Time Series Insights usando C #

Este artigo demonstra como combinar C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)e Azure Active Directory para fazer solicitações programáticas de API para a [API de gerenciamento de dados de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)ga da série de tempo do Azure .

> [!TIP]
> Exibir amostras de código [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)GA C# em .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Adquirindo um token de acesso usando [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**.
* Operações seqüenciais CREATE, READ, UPDATE e DELETE contra a [API de gerenciamento de dados de referência ga](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
* Códigos de resposta comuns, incluindo [códigos de erro comuns.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling)
    
    A API de gerenciamento de dados de referência processa cada item individualmente e um erro com um item não impede que os outros sejam concluídos com sucesso. Por exemplo, se sua solicitação tiver 100 itens e um item tiver um erro, 99 itens serão escritos e um será rejeitado.

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisão um ambiente GA Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
)

1. [Crie um conjunto de dados de referência](time-series-insights-add-reference-data-set.md) dentro do seu ambiente. Use o seguinte esquema de dados de referência:

   | Nome da chave | Type |
   | --- | --- |
   | uuid | String | 

1. Configure o ambiente Azure Time Series Insights para o Azure Active Directory conforme descrito em [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). Use `http://localhost:8080/` como **uri de redirecionamento**.

1. Instale as dependências de projeto necessárias.

1. Edite o código de amostra abaixo substituindo cada **#PLACEHOLDER#** pelo identificador de ambiente apropriado.

1. Execute `dotnet run` dentro do diretório raiz do seu projeto. Quando solicitado, use seu perfil de usuário para fazer login no Azure. 

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que você use a versão mais recente do Visual Studio e **do NETCore.app**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) - Versão 2.2.8

O código amostral tem duas dependências necessárias:

* MSAL.NET [pacote Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) - 4.7.1.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - pacote 12.0.3.

Adicione os pacotes usando [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Ou:

1. Declare `csharp-tsi-msal-ga-sample.csproj` um arquivo:

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. Em seguida, execute `dotnet restore`.

## <a name="c-sample-code"></a>Código de exemplo C#

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Leia a documentação de referência [da API de gerenciamento de dados de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) da GA.
