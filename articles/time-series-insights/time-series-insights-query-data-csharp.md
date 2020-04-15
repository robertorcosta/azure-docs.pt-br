---
title: Consultar dados de um ambiente GA usando o código C# - Azure Time Series Insights | Microsoft Docs
description: Aprenda a consultar dados de um ambiente Azure Time Series Insights usando um aplicativo personalizado escrito em C#.
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383880"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Consultar dados do ambiente GA do Azure Time Series Insights usando C #

Este exemplo C# demonstra como usar as [APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) de consulta ga para consultar dados de ambientes GA Insights GA do Azure Time Series.

> [!TIP]
> Exibir amostras de código [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)GA C# em .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Como adquirir um token de acesso através do Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar esse token de `Authorization` acesso adquirido no cabeçalho das solicitações subsequentes da API de Consulta. 

* A amostra chama cada uma das APIs de consulta ga demonstrando como as solicitações HTTP são feitas para:
    * [Obtenha a API de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) para devolver os ambientes aos que o usuário tem acesso
    * [Obtenha API de disponibilidade de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Obtenha a API de metadados de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) para recuperar metadados do ambiente
    * [Obter API de eventos de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Obtenha API agregados de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Como interagir com as APIs de consulta ga usando o WSS para enviar a mensagem:

   * [Obtenha aAPI de eventos ambientais transmitidos](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Obtenha aPI streamed de agregados de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisão um ambiente GA Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Configure o ambiente Azure Time Series Insights para o Azure Active Directory conforme descrito em [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Instale as dependências de projeto necessárias.
1. Edite o código de amostra abaixo substituindo cada **#DUMMY#** pelo identificador de ambiente apropriado.
1. Execute o código dentro do Visual Studio.

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que você use a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código amostral tem duas dependências necessárias:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - pacote 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - pacote 9.0.1.

Baixe os pacotes no Visual Studio 2019 selecionando a opção **Build** > **Build Solution.**

Alternativamente, adicione os pacotes usando [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Código de exemplo C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consulta, leia a [referência da API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) ao Time Series Insights.
