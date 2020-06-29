---
title: Consultar dados de um ambiente de Versão Prévia usando o C# – Azure Time Series Insights | Microsoft Docs
description: Saiba como consultar dados de um ambiente do Azure Time Series Insights usando um aplicativo escrito em C#.
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
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379822"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consulte dados do ambiente de Versão Prévia do Azure Time Series Insights usando o C#

Este exemplo em C# demonstra como consultar dados das [APIs de Acesso a Dados da Versão Prévia](https://docs.microsoft.com/rest/api/time-series-insights/preview) em ambientes de Versão Prévia do Azure Time Series Insights.

> [!TIP]
> Veja os exemplos de código C# da Versão Prévia em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Resumo

O código de exemplo abaixo demonstra os seguintes recursos:

* Suporte para geração automática do SDK por meio do [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso por meio do Azure Active Directory usando [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar esse token de acesso adquirido no cabeçalho `Authorization` das solicitações seguintes da API de Acesso a Dados. 
* A amostra fornece uma interface de console que demonstra como as solicitações HTTP são feitas à:

    * [API de Ambientes da Versão Prévia](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [API de Obtenção da Disponibilidade de Ambientes](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [API de Obtenção do Esquema de Evento](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [API de Consulta da Versão Prévia](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [API de Obtenção de Eventos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [API de Obtenção da Série](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries) e [API de Obtenção da Série Agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [APIs de Modelo de Série Temporal](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [API de Obtenção de Hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [API do Lote de Hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [API de Obtenção de Tipos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e [API do Lote de Tipos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [API de Obtenção de Instâncias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) e [API do Lote de Instâncias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Funcionalidades avançadas de [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) e [TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de usar compilar e executar o código de exemplo:

1. [Provisionar um ambiente de Versão Prévia do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment).
1. Configurar o ambiente do Azure Time Series Insights para o Azure Active Directory, conforme descrito em [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Executar o [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat), conforme especificado no [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) para gerar as dependências do cliente de Versão Prévia do Time Series Insights.
1. Abrir a solução `TSIPreviewDataPlaneclient.sln` e definir `DataPlaneClientSampleApp` como o projeto padrão no Visual Studio.
1. Instalar as dependências de projeto necessárias usando as etapas descritas [abaixo](#project-dependencies) e compilar o exemplo em um arquivo `.exe` executável.
1. Executar o arquivo `.exe` clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências do projeto

Recomendamos usar a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/): versão 16.4.2 e posterior

O código de exemplo tem várias dependências necessárias que podem ser exibidas no arquivo [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config).

Baixe os pacotes no Visual Studio 2019 selecionando a opção **Compilar** > **Compilar Solução**. 

Como alternativa, adicione cada pacote usando o [NuGet 2.12 e posterior](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de exemplo C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * O exemplo de código pode ser executado sem alterar as variáveis de ambiente padrão.
> * O exemplo de código será compilado em um aplicativo de console executável .NET.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consultas, leia a [referência da API de Consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) ao Time Series Insights.
