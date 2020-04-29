---
title: Consultar dados de um ambiente GA usando código C#-Azure Time Series Insights | Microsoft Docs
description: Saiba como consultar dados de um ambiente de Azure Time Series Insights usando um aplicativo personalizado escrito em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383880"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Consultar dados do ambiente Azure Time Series Insights GA usando C #

Este exemplo de C# demonstra como usar as [APIs de consulta GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) para consultar dados de ambientes Azure Time Series insights ga.

> [!TIP]
> Veja exemplos de código do GA [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)em C# em.

## <a name="summary"></a>Resumo

O código de exemplo a seguir demonstra os seguintes recursos:

* Como adquirir um token de acesso por meio de Azure Active Directory usando [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar esse token de acesso adquirido no `Authorization` cabeçalho de solicitações de API de consulta subsequentes. 

* O exemplo chama cada uma das APIs de consulta GA demonstrando como as solicitações HTTP são feitas ao:
    * [Obter a API de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) para retornar os ambientes aos quais o usuário tem acesso
    * [Obter API de disponibilidade do ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Obter API de metadados de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) para recuperar metadados de ambiente
    * [Obter API de eventos de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Obter API de agregações de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Como interagir com as APIs de consulta GA usando o WSS para a mensagem:

   * [Obter API de fluxo de eventos de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Obter a API transmitida de agregações de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisione um ambiente de Azure Time Series insights GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .
1. Configure seu ambiente de Azure Time Series Insights para Azure Active Directory conforme descrito em [autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Instale as dependências de projeto necessárias.
1. Edite o código de exemplo abaixo substituindo cada **#DUMMY #** pelo identificador de ambiente apropriado.
1. Execute o código dentro do Visual Studio.

## <a name="project-dependencies"></a>Dependências do projeto

É recomendável que você use a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versão 16.4.2 +

O código de exemplo tem duas dependências necessárias:

* Pacote [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9.
* Pacote [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1.

Baixe os pacotes no Visual Studio 2019 selecionando a opção **criar** > **solução de compilação** .

Como alternativa, adicione os pacotes usando o [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Código de exemplo C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.
