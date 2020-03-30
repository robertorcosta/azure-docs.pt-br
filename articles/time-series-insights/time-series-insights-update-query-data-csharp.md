---
title: Consultar dados de um ambiente de visualização usando C# - Azure Time Series Insights | Microsoft Docs
description: Aprenda a consultar dados de um ambiente Azure Time Series Insights usando um aplicativo escrito em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980980"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consultar dados do ambiente azure Time Series Insights Preview usando C #

Este exemplo C# demonstra como consultar dados das APIs de acesso a [dados de visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) nos ambientes de visualização de visualização da série de tempo do Azure.

> [!TIP]
> Exibir amostras de código [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)Pré-visualização C# em .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Suporte para geração automática SDK do [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso através do Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar esse token de `Authorization` acesso adquirido no cabeçalho das solicitações subsequentes de API de acesso a dados. 
* A amostra fornece uma interface de console demonstrando como as solicitações HTTP são feitas para:

    * [API de visualização de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Obtenha API de disponibilidade de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [obtenha API de esquema de eventos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [API de consulta de visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Obtenha API de eventos,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [obtenha API série](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)e [obtenha API de série agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [APIs modelo de série sinuosa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Obtenha API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) de hierarquias e [API de lote de hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Obtenha aPI](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) de tipos e [aPI de lote de tipos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Obtenha API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) de exemploes e [API de lote de instâncias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Recursos avançados [de pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) e [TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisão de um ambiente de insights da série de tempo do Azure.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Configure o ambiente Azure Time Series Insights para o Azure Active Directory conforme descrito em [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Execute o [GerarCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) para gerar as dependências do cliente de visualização de visualização de visualização de séries tempo.
1. Abra `TSIPreviewDataPlaneclient.sln` a solução `DataPlaneClientSampleApp` e defina como o projeto padrão no Visual Studio.
1. Instale as dependências de projeto necessárias usando as etapas descritas [abaixo](#project-dependencies) e compile o exemplo a um arquivo executável. `.exe`
1. Execute `.exe` o arquivo clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que você use a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código de amostra tem várias dependências necessárias que podem ser visualizadas no arquivo [packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Baixe os pacotes no Visual Studio 2019 selecionando a opção **Build** > **Build Solution.** 

Alternativamente, adicione cada pacote usando [NuGet 2.12+](https://www.nuget.org/). Por exemplo: 

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de exemplo C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A amostra de código pode ser executada sem alterar as variáveis de ambiente padrão.
> * A amostra de código será compilada para um aplicativo de console executável .NET.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consulta, leia a [referência da API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) ao Time Series Insights.
