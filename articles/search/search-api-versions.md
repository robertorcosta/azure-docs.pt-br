---
title: Versões de API
titleSuffix: Azure Cognitive Search
description: Política de versão para as APIs REST do Azure Pesquisa Cognitiva e a biblioteca de cliente no SDK do .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: bf3e4262f4342788f343ab287fd3db53d12736c7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918104"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versões de API no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva distribui atualizações de recursos regularmente. Às vezes, mas não sempre, essas atualizações exigem uma nova versão da API para preservar a compatibilidade com versões anteriores. A publicação de uma nova versão permite controlar quando e como as atualizações de serviço de pesquisa são integradas ao seu código.

Como regra, a equipe de Pesquisa Cognitiva do Azure publica novas versões somente quando necessário, pois ela pode envolver algum esforço para atualizar seu código para usar uma nova versão de API. Uma nova versão será necessária somente se alguns aspectos da API tiverem sido alterados de uma forma que interrompa a compatibilidade com versões anteriores. Essas alterações podem ocorrer devido a correções a recursos existentes ou devido a recursos novos que alteram a área da superfície da API existente.

A mesma regra se aplica às atualizações do SDK. O SDK do Pesquisa Cognitiva do Azure segue as regras de [controle de versão semântica](https://semver.org/) , o que significa que sua versão tem três partes: principal, secundária e número de compilação (por exemplo, 1.1.0). Uma nova versão principal do SDK será liberada somente no caso de alterações que interrompam a compatibilidade com versões anteriores. Para atualizações contínuas de recursos, incrementaremos a versão secundária, e para correções de bug, só aumentaremos a versão de build.

> [!Important]
> Os SDKs do Azure para .NET, Java, Python e JavaScript estão distribuindo novas bibliotecas de cliente para o Azure Pesquisa Cognitiva. No momento, nenhuma das bibliotecas do SDK do Azure dá suporte total às APIs REST de pesquisa mais recentes (2020-06-30) ou às APIs REST de gerenciamento (2020-03-13), mas isso mudará ao longo do tempo. Você pode verificar periodicamente esta página ou o [que há de novo](whats-new.md) para anúncios em aprimoramentos funcionais. 

## <a name="rest-apis"></a>APIs REST

Uma instância do serviço de Pesquisa Cognitiva do Azure dá suporte a várias versões da API REST, incluindo a mais recente. Você pode continuar a usar uma versão quando ela não for mais a mais recente, mas é recomendável [migrar seu código](search-api-migration.md) para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

A tabela a seguir fornece o histórico de versão das versões atuais e lançadas anteriormente da API REST do Serviço de Pesquisa. A documentação é publicada somente para as versões mais recentes do estável e de visualização.

### <a name="search-service-apis"></a>APIs de Serviço de Pesquisa

Criar e gerenciar conteúdo em um serviço de pesquisa.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problema de compatibilidade com versões anteriores |
|-------------------------|--------|------------------------------|
| [Pesquisar 2020-06-30](/rest/api/searchservice/index)| Estável | Versão estável mais recente das APIs REST de pesquisa, com avanços na pontuação de relevância e geralmente a disponibilidade para a loja de conhecimento.|
| [Pesquisar 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Visualização | Versão de visualização associada à versão estável. Inclui vários [recursos de visualização](search-api-preview.md). |
| Pesquisar 2019-05-06 | Estável | Adiciona [tipos complexos](search-howto-complex-data-types.md). |
| Pesquisa 2019-05-06-Versão Prévia | Visualização | Versão de visualização associada à versão estável. |
| Pesquisar 2017-11-11 | Estável  | Adiciona habilidades e [enriquecimento de ia](cognitive-search-concept-intro.md). |
| Pesquisa 2017-11-11-visualização | Visualização | Versão de visualização associada à versão estável. |
| Pesquisar 2016-09-01 |Estável | Adiciona [indexadores](search-indexer-overview.md). |
| Pesquisa 2016-09-01-visualização | Visualização | Versão de visualização associada à versão estável.|
| Pesquisar 2015-02-28 | Estável  | Primeira versão disponível em geral.  |
| Pesquisa 2015-02-28-visualização | Visualização | Versão de visualização associada à versão estável. |
| Pesquisa 2014-10-20-visualização | Visualização | Segunda visualização pública. |
| Pesquisa 2014-07-31-visualização | Visualização | Primeira visualização pública. |

### <a name="management-rest-apis"></a>APIs REST de gerenciamento

Criar e configurar um serviço de pesquisa e gerenciar chaves de API.

| Versão&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problema de compatibilidade com versões anteriores |
|-------------------------|--------|------------------------------|
| [Gerenciamento 2020-03-13](/rest/api/searchmanagement/) | Estável | Versão estável mais recente das APIs REST de gerenciamento, com avanços no Endpoint Protection. Adiciona um [ponto de extremidade privado](service-create-private-endpoint.md) por meio de link privado e [regras de IP de rede](service-configure-firewall.md) para novos serviços. |
| [Gerenciamento 2019-10-01-Preview](/rest/api/searchmanagement/index-2019-10-01-preview) | Visualização  | Apesar de seu número de versão, ainda é a versão de visualização atual das APIs REST de gerenciamento. Não há recursos de visualização no momento. Todos os recursos de visualização passaram por transição para a disponibilidade geral. |
| Gerenciamento 2015-08-19  | Estável | A primeira versão disponível geral das APIs REST de gerenciamento. Fornece provisionamento de serviço, escala vertical e gerenciamento de chaves de API. |
| Gerenciamento 2015-08-19-visualização | Visualização | A primeira versão de visualização das APIs REST de gerenciamento. |

## <a name="azure-sdk-for-net"></a>SDK do Azure para .NET

O histórico de versão do pacote está disponível em NuGet.org. Esta tabela fornece links para cada página de pacote.


| Versão do SDK | Status | Descrição |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Destina-se à API REST de pesquisa-Version = 2020-06-30, e ainda não oferece suporte a, filtros geográficos ou [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft. Azure. Search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Estável | Lançada em maio de 2019. Tem como destino a API REST de pesquisa-Version = 2019-05-06.|
| [Microsoft. Azure. Search 8,0-Preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Visualização | lançada em abril de 2019. Tem como destino a API REST de pesquisa-Version = 2019-05-06-Preview.|
| [Microsoft. Azure. Management. Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19.  |

## <a name="azure-sdk-for-java"></a>SDK do Azure para Java

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [Java Azure-Search-Documents 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2019-05-06. |
| [1.35.0 do cliente de gerenciamento Java](/java/api/overview/azure/search/management?view=azure-java-stable) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>SDK do Azure para JavaScript

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [JavaScript Azure-Search 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2016-09-01. |
| [JavaScript Azure-ARM-Search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>SDK do Azure para Python

| Versão do SDK | Status | Descrição  |
|-------------|--------|------------------------------|
| [Python Azure-Search-Documents 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Estável | Nova biblioteca de cliente do SDK do .NET do Azure, lançada em julho de 2020. Tem como destino a API REST de pesquisa-Version = 2019-05-06. |
| [Python Azure-MGMT-Search 1,0](/python/api/overview/azure/search?view=azure-python) | Estável | Destina-se à API REST de gerenciamento-versão = 2015-08-19. |