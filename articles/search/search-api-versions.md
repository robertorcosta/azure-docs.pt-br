---
title: Versões de API
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830087"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se alguns aspectos da API tiverem sido alterados de uma forma que interrompa a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

A mesma regra se aplica às atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK será liberada somente no caso de alterações que interrompam a compatibilidade com versões anteriores. Para atualizações contínuas de recursos, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!NOTE]
> Uma instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

## <a name="rest-apis"></a>APIs REST

Esta tabela fornece o histórico de versão das versões atual e anteriormente lançadas da API REST do Serviço de Pesquisa. A documentação é publicada para as versões atuais estáveis e de visualização.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problema de compatibilidade com versões anteriores |
|-------------|--------|------------------------------|
| [Gerenciamento 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Disponível | Versão estável mais recente das APIs REST de gerenciamento, com avanços no Endpoint Protection. Adiciona ponto de extremidade privado, suporte a link privado e regras de rede para novos serviços. |
| [Gerenciamento 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Visualização  | Apesar de seu número de versão, ainda é a versão de visualização atual das APIs REST de gerenciamento. Não há recursos de visualização no momento. Todos os recursos de visualização passaram por transição para a disponibilidade geral. |
| Gerenciamento 2015-08-19  | Estável| A primeira versão disponível geral das APIs REST de gerenciamento. Fornece provisionamento de serviço, escala vertical e gerenciamento de chaves de API. |
| Gerenciamento 2015-08-19-visualização | Visualização| A primeira versão de visualização das APIs REST de gerenciamento. |
| [Pesquisar 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Estável | Versão estável mais recente das APIs REST de pesquisa de pesquisa, com avanços na pontuação de relevância. |
| [Pesquisar 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Visualização | Versão de visualização associada à versão estável. |
| Pesquisar 2019-05-06 | Estável | Adiciona tipos complexos. |
| Pesquisa 2019-05-06-Versão Prévia | Visualização | Versão de visualização associada à versão estável. |
| Pesquisar 2017-11-11 | Estável  | Adiciona habilidades e enriquecimento de ia. |
| Pesquisa 2017-11-11-visualização | Visualização | Versão de visualização associada à versão estável. |
| Pesquisar 2016-09-01 |Estável | Adiciona indexadores|
| Pesquisa 2016-09-01-visualização | Visualização | Versão de visualização associada à versão estável.|
| Pesquisar 2015-02-28 | Estável  | Primeira versão disponível em geral.  |
| Pesquisa 2015-02-28-visualização | Visualização | Versão de visualização associada à versão estável. |
| Pesquisa 2014-10-20-visualização | Visualização | Segunda visualização pública. |
| Pesquisa 2014-07-31-visualização | Visualização | Primeira visualização pública. |

## <a name="azure-sdk-for-net"></a>SDK do Azure para .NET

O histórico de versão do pacote está disponível em NuGet.org. Esta tabela fornece links para cada página de pacote.

| Versão do SDK | Status | Descrição |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-Preview. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Visualização | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em maio de 2020. Tem como alvo a versão da API REST 2020-06-30|
| [**Microsoft. Azure. Search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Disponível em geral, lançada em 2019 de maio. Tem como alvo a versão da API REST 2019-05-06.|
| [**Microsoft. Azure. Search 8,0-Preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Versão prévia, lançada em abril de 2019. Tem como alvo a versão da API REST 2019-05-06-Preview.|
| [**Microsoft. Azure. Management. Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |

## <a name="azure-sdk-for-java"></a>SDK do Azure para Java

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [**1.35.0 SearchManagementClient Java**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19.|

## <a name="azure-sdk-for-python"></a>SDK do Azure para Python

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [**Python Azure-MGMT-Search 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |