---
title: Consultar dados de um ambiente de visualização usando C#-Azure Time Series Insights | Microsoft Docs
description: Saiba como consultar dados de um ambiente de Azure Time Series Insights usando um aplicativo escrito em C#.
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
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379822"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consultar dados do ambiente de visualização de Azure Time Series Insights usando C #

Este exemplo de C# demonstra como consultar dados das [APIs de acesso a dados de visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) em ambientes Azure Time Series insights Preview.

> [!TIP]
> Exiba exemplos de código C# de [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)visualização em.

## <a name="summary"></a>Resumo

O código de exemplo a seguir demonstra os seguintes recursos:

* Suporte para geração automática do SDK do [Azure autorest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso por meio de Azure Active Directory usando [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar esse token de acesso adquirido no `Authorization` cabeçalho de solicitações de API de acesso a dados subsequentes. 
* O exemplo fornece uma interface de console que demonstra como as solicitações HTTP são feitas ao:

    * [API de visualização de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Obter API de disponibilidade de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [obter API de esquema de evento](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Visualizar API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Obter](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)API de eventos, [obter a API da série Get](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)e [obter a API da série agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [APIs de modelo de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * API do lote obter hierarquias [API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * API de tipos [Get](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e API do [lote de tipos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * API de Get de instâncias e [API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) do [lote](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch) de instâncias
* Recursos de [pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) avançada e [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) .

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisionar um ambiente de Azure Time Series insights de visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) .
1. Configure seu ambiente de Azure Time Series Insights para Azure Active Directory conforme descrito em [autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Execute o [GenerateCode. bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [README.MD](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) para gerar as dependências de cliente do time Series insights Preview.
1. Abra a `TSIPreviewDataPlaneclient.sln` solução e defina `DataPlaneClientSampleApp` como o projeto padrão no Visual Studio.
1. Instale as dependências de projeto necessárias usando as etapas descritas [abaixo](#project-dependencies) e compile o exemplo `.exe` em um arquivo executável.
1. Execute o `.exe` arquivo clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências do projeto

É recomendável que você use a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versão 16.4.2 +

O código de exemplo tem várias dependências necessárias que podem ser exibidas no arquivo [Packages. config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config) .

Baixe os pacotes no Visual Studio 2019 selecionando a opção **criar** > **solução de compilação** . 

Como alternativa, adicione cada pacote usando o [NuGet 2.12 +](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de exemplo C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * O exemplo de código pode ser executado sem alterar as variáveis de ambiente padrão.
> * O exemplo de código será compilado em um aplicativo de console executável .NET.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.
